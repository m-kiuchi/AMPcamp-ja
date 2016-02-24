# 2. Spark SQL
Spark SQLはSparkの中でも最も新しいコンポーネントであり、SQLに似たインターフェイスを提供します。

まずSpark Shellを起動します。

```
$ cd ${HOME}
$ SPARK_MEM=4g spark-1.6.0/bin/spark-shell
```

起動したSpark ShellでSQLContextを宣言します。

```
scala> val sqlContext = new org.apache.spark.sql.SQLContext(sc)
sqlContext: org.apache.spark.sql.SQLContext = org.apache.spark.sql.SQLContext@7074da1d
scala> sqlContext.setConf("spark.sql.parquet.binaryAsString", "true")
scala> 
```

これでParquetフォーマットのデータを読み出す準備ができました。
Parquetフォーマットとはカラム型データベースParquet[[1]](#[1])で使用されるフォーマットです。
Spark SQLはParquetフォーマットを自動的に解釈し、カラムの名前、データフォーマットを理解します。
この練習ではあらかじめ用意した、Wikipediaの”berkeley”という文字が含まれる全てのページのデータを使用します。

```
scala> val wikiData = sqlContext.parquetFile("training/data/wiki_parquet")
wikiData: org.apache.spark.sql.DataFrame = [id: int, title: string, modified: bigint, text: string, username: string]
```

読み込まれた結果は[DataFrameクラス](http://spark.apache.org/docs/latest/sql-programming-guide.html#dataframes)のデータになっており、DataFrameクラスのメソッドを使用することができます。試しに何個のデータが入っているかをカウントしてみましょう。

```
scala> wikiData.count()
res0: Long = 39365
```

標準的なDataFrameクラスのメソッドに加えて、データセットの中のデータのカラム名や、タイプについてもSQLクエリーで検索・表示することができます。それではやってみましょう。

```
scala> wikiData.registerTempTable("wikiData")
scala> val contResult = sqlContext.sql("SELECT COUNT(*) FROM wikiData").collect()
contResult: Array[org.apache.spark.sql.Row] = Array([39365])
scala> val sqlCount = contResult.head.getLong(0)
sqlCount: Long = 39365
```

上記の”wikiData.count()”で取得した”39365”と同じデータ数がSQLで取得できたことがわかります

もう少し実践的なSQLを実行してみましょう。wikiDataテーブルは以下のようなフォーマットで構成されています。

| カラム名 | 型       |
|:---------|:---------|
| id       | int      |
| title    | string   |
| modified | bigint   |
| text     | string   |
| username | string   |


カラム”username”でグルーピングし、上位10行を取り出します。つまり、wikiDataの更新者のTop10を抽出します。

以下が実際のSQLになります。

```
scala> sqlContext.sql("SELECT username, COUNT(*) AS cnt FROM wikiData WHERE username <> '' GROUP BY username ORDER BY cnt DESC LIMIT 10").collect().foreach(println)
[Waacstats,2003]
[Cydebot,949]
[BattyBot,939]
[Yobot,890]
[Addbot,853]
[Monkbot,668]
[ChrisGualtieri,438]
[RjwilmsiBot,387]
[OccultZone,377]
[ClueBot NG,353]

scala> 
```

------------------------------
<a id="[1]"></a>
[1] [Apache Parquet](http://parquet.apache.org/): カラム型データをHadoop内部に持つことができるデータベース (参考: [http://www.publickey1.jp/blog/13/hadoopparquettwitter.html](http://www.publickey1.jp/blog/13/hadoopparquettwitter.html) )
