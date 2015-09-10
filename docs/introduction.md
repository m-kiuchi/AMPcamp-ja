# 1. はじめに


本文書は、[Spark Summit 2014 Training hands-on exercises](https://databricks-training.s3.amazonaws.com/index.html), および[AMP Camp 5 - big data bootcamp](http://ampcamp.berkeley.edu/5/)を元に簡易に手順を記述し、自習者がApache Sparkをより簡便に理解する手助けをするための文章です。
元のドキュメントから現行バージョン(1.4.1)に合わせて少し記述を変更している部分があります。本文書は、JDK 8 Update 45(Oracle公式版)、Spark 1.4.1 (2015/8/24時点の最新版)を前提に記述されています。

## 1-1. 用意するもの


Linuxマシン：以下の要件を満たしていること
物理でも仮想でも可能

- CPU: 2core以上(4core以上を推奨)
- MEM: 4GB以上(6GB以上を推奨)
- CentOS7以降(x86_64)
- Java(JDK) 6もしくは7がインストールされていること(JDK 8 Update 60以降を推奨)
   - 【注意】
     - JREはNG。以下のコマンドが正常に完了しない
     - OpenJDKもNG。一見正常に終了するものの個々のトレーニングでは挙動が意図したとおりにならないことが多い。Oracle公式パッケージを使用すること

```
# yum -y update
# yum -y install wget unzip

最新のJDKパッケージをアップロード
# yum --nogpgcheck localinstall jdk-8u60-linux-x64.rpm

rootから一般ユーザに変更
$ wget http://ftp.riken.jp/net/apache/spark/spark-1.4.1/spark-1.4.1.tgz
$ tar xzvf spark-1.4.1.tgz
$ cd spark-1.4.1
$ build/mvn -Pyarn -Phadoop-2.4 -Dhadoop.version=2.4.0 -DskipTests clean package
(所要時間: 約30～50分)
$ wget https://dl.bintray.com/sbt/native-packages/sbt/0.13.9/sbt-0.13.9.zip
$ unzip sbt-0.13.9.zip
$ cd sbt/bin
$ ln sbt-launch.jar sbt-launch-0.13.9.jar
$ cd ${HOME}/spark-1.4.1/conf
$ cp log4j.properties.template log4j.properties
$ vi log4j.properties
clog4j.rootCategory=WARN, console

トレーニング用のデータを用意します
$ cd ${HOME}
$ mkdir training
$ cd training
$ wget http://d12yw77jruda6f.cloudfront.net/ampcamp5-usb.zip
$ unzip ampcamp5-usb.zip
$ wget https://databricks-training.s3.amazonaws.com/training-downloads.zip
$ unzip training-downloads.zip

*SparkRを使う場合はこちらのURLが必要です
$ wget http://d12yw77jruda6f.cloudfront.net/training-downloads.zip 
$ unzip training-downloads.zip
```

## 1-2. トレーニングの概要
Sparkの機能についてトレーニングを行います。それぞれの機能を使用するにあたって、以下の言語の知識を必要とします。

|                           |Scala     |Java      |Python     |
|:--------------------------|:---------|:---------|:----------|
|インタラクティブSpark SQL  |YES       |NO        |YES        |
|Sparkストリーミング        |YES       |YES       |NO         |
|機械学習 - MLlib           |YES       |NO        |YES        |
|グラフ分析 - GraphX        |YES       |NO        |NO         |
|SparkR                     |Rのみ     |Rのみ     |Rのみ      |
|Pipeline                   |YES       |NO        |NO         |
|Tachyon                    |NO        |YES       |NO         |

それでは実際に個々の機能のトレーニングを始めましょう。
以下の各項目のリンクをクリックすると実際のトレーニングに移動します。
それぞれのトレーニングの分量を参考にしてください。



|                    |分量    |参考資料                                                                                    |
|:-------------------|:-------|:-------------------------------------------------------------------------------------------|
|Spark SQL           |少      |[プログラミングガイド](http://spark.apache.org/docs/latest/sql-programming-guide.html)      |
|Sparkストリーミング |中      |[プログラミングガイド](http://spark.apache.org/docs/latest/streaming-programming-guide.html)|
|MLlib(機械学習)     |中      |[プログラミングガイド](http://spark.apache.org/docs/latest/mllib-guide.html)                |
|GraphX(グラフ分析)  |多      |[プログラミングガイド](http://spark.apache.org/docs/latest/sql-programming-guide.html)      |
|SparkR              |少      |                                                                                            |
|Pipeline            |多      |                                                                                            |
|Tachyon             |中      |[プロジェクトサイト](http://tachyon-project.org/)                                           |
