# spark

## start master & slave

```bash
# download
wget http://mirror.bit.edu.cn/apache/spark/spark-2.4.0/spark-2.4.0-bin-hadoop2.7.tgz
wget http://mirrors.tuna.tsinghua.edu.cn/apache/spark/spark-2.4.3/spark-2.4.3-bin-hadoop2.7.tgz

# unzip
tar xf spark-2.4.0-bin-hadoop2.7.tgz
cd spark-2.4.0-bin-hadoop2.7

# start master
./sbin/start-master.sh

# start slave
./sbin/start-slave.sh spark://my_ip:7077

# path
export PATH=/data/spark/bin:$PATH
```

## install on mac

```bash
# check java version
java -version

# install java 8 on mac
brew tap caskroom/versions
brew cask install java8

# install scala
brew install scala

# install apache
brew install apache-spark
# to upgrade to 2.4.0
brew upgrade apache-spark
# show current version
brew info apache-spark
# switch to 2.3.1
brew switch apache-spark 2.3.1
# list all available version
brew list apache-spark --versions

# install specific version via formula
# find commit
cd "$(brew --repo homebrew/core)"
git log Formula/apache-spark.rb
# 2.3.2 0f3992a1e6d036dab8b420903d856231e7701ba1
# or use github history
# then download formula rb https://github.com/Homebrew/homebrew-core/blob/0f3992a1e6d036dab8b420903d856231e7701ba1/Formula/apache-spark.rb
# modify line4 to a correct url "http://archive.apache.org/dist/spark/spark-2.3.2/spark-2.3.2-bin-hadoop2.7.tgz"
# save rb to local and install via it
brew install ./apache-spark.rb
```

## spark-shell

```bash
# open shell
spark-shell
spark-shell --executor-memory 9g --total-executor-cores 9 --num-executors 9 --driver-memory 9g
spark-shell --executor-memory 4g --total-executor-cores 8 --num-executors 8 --driver-memory 4g

# local
spark-shell --master local[*]

# yarn
spark-shell --master yarn-client

# open shell with es
# use wan.only to turn off auto lookup
spark-shell --jars [/path/to/elasticsearch-hadoop-6.5.4.jar] --conf spark.es.nodes="my_elasticsearch_ip" --conf spark.es.port=9200 --conf spark.es.nodes.wan.only=true

# open shell with cassandra
spark-shell \
--jars [/path/to/spark-cassandra-connector_2.11-2.3.2.jar,/path/to/jsr166e-1.1.0.jar] \
--conf spark.cassandra.connection.host=<my_ip1>,<my_ip2>

# open shell with jdbc
spark-shell --jars [/path/to/mysql-connector-java-5.1.24.jar]

# open shell with application.conf
spark-shell --jars [/path/to/config-1.4.0.jar] --files 'application.conf'

# multiple line mode
:paste
```

```scala
// read conf
import com.typesafe.config.ConfigFactory
import java.io.File
import org.apache.spark.SparkFiles

val config = ConfigFactory.parseFile(new File(SparkFiles.get("application.conf")))
```

## spark-submit

```bash
# check spark version
spark-submit --version

# submit job to local
spark-submit --class my_main_class --master local[4] path_to_my_jar my_param_1 my_param_2

# submit job to cluster
spark-submit --master spark://my_cluster_ip:7077 --executor-memory 2G --total-executor-cores 8 --num-executors 4 --class my_main_class path_to_my_jar my_param_1 my_param_2

# submit job with hdfs & es
HADOOP_USER_NAME=root spark-submit --master local[4] --num-executors 4 --conf spark.es.nodes="my_elasticsearch_ip" --conf spark.es.port=9200 --conf spark.es.nodes.wan.only=true --class my_main_class path_to_my_jar my_param_1 my_param_2

# submit job in python to cluster
spark-submit --master spark://my_cluster_ip:7077 --executor-memory 2G --total-executor-cores 8 --num-executors 50 my_job.py

# submit job in python to local
spark-submit --master local[4] my_job.py
spark-submit \
  --master local[4] \
  --driver-memory 4G --executor-memory 4G  \
  --num-executors 4 --total-executor-cores 8 \
  --conf spark.driver.maxResultSize=2G \
  my_job.py
```

## spark-shell

### fs

```scala
// check status
import org.apache.hadoop.fs.{FileSystem,Path}
import org.apache.commons.io.FileUtils

val statuses = FileSystem.get( sc.hadoopConfiguration ).listStatus(new Path("/user"))

for (status <- statuses) {
    println(status.toString())
    println(FileUtils.byteCountToDisplaySize(status.getLen()))
}

// delete folder
import org.apache.hadoop.fs.{FileSystem,Path}
import org.apache.commons.io.FileUtils
import java.net.URI

val fs = FileSystem.get(URI.create("s3a://../"), conf)
val outPutPath = new Path("s3a://.../")
if (fs.exists(outPutPath))
  fs.delete(outPutPath, true)
```

### time

```scala
spark.time(
// code
)
```

### function

```scala
// ts => seconds
unix_timestamp(col_time_string: Column) : col_number_of_seconds: Column
unix_timestamp(col_time_string: Column, string_pattern: String) : col_number_of_seconds: Column
unix_timestamp($"ts", "yyyy-MM-dd HH:mm:ss Z")
// seconds => ts
from_unixtime(col_number_of_seconds: Column) : col_time_string: Column
from_unixtime($"seconds")
// tz  ts => utc ts
to_utc_timestamp(col_timezone_time_string: Column, timezone: String) : col_utc_time_string: Column
to_utc_timestamp($"ts", "CST")
// utc ts => tz ts
from_utc_timestamp(col_utc_time_string: Column, timezone: String) : col_timezone_time_string: Column
from_utc_timestamp($"utc_ts", "EST")

java.sql.Timestamp.valueOf("2016-05-09 10:12:43")

// format
date_format($"col_timestamp","dd/MM/yyyy")
```

### size

```scala
import org.apache.spark.util.SizeEstimator
SizeEstimator.estimate(df)
```

### explain physical plan

```scala
spark.sql("select * from db.table").explain
```

### write to mysql

```scala
import java.util.{Properties}

val url = "jdbc:mysql://ip/mydb?" + "characterEncoding=UTF-8&useSSL=false&useUnicode=true"
val props = new Properties()
props.put("user", "username")
props.put("password", "password")

spark.sql("""
select * from db.table
""").write.mode("overwrite").jdbc(url, "mytable", props)
```

### json

```scala
// dynamic schema
val df = spark.read.parquet("<path_to_dir>")
val schema = schema_of_json(df.first().getAs[String]("json_str"))
val df2 = df.withColumn("parsed", from_json(col("json_str"),schema))
df2.coalesce().write.parquet("<path_to_output>")
```

## SimpleDateFormat

- https://docs.oracle.com/javase/7/docs/api/java/text/SimpleDateFormat.html

| SimpleDateFormat | Spark           | Date or Time Component                           | Examples                                    |
| ---------------- | --------------- | ------------------------------------------------ | ------------------------------------------- |
| `G`              | G               | Era designator                                   | `AD`                                        |
| `y`              | y               | Year                                             | `1996`; `96`                                |
| `Y`              |                 | Week year                                        | `2009`; `09`                                |
| `M`              | M               | Month in year                                    | `July`; `Jul`; `07`                         |
| `w`              | w               | Week in year                                     | `27`                                        |
| `W`              | W               | Week in month                                    | `2`                                         |
| `D`              | D               | Day in year                                      | `189`                                       |
| `d`              | d               | Day in month                                     | `10`                                        |
| `F`              | F               | Day of week in month                             | `2`                                         |
| `E`              | E               | Day name in week                                 | `Tuesday`; `Tue`                            |
| `u`              |                 | Day number of week (1 = Monday, ..., 7 = Sunday) | `1`                                         |
| `a`              | a               | Am/pm marker                                     | `PM`                                        |
| `H`              | H               | Hour in day (0-23)                               | `0`                                         |
| `k`              | k               | Hour in day (1-24)                               | `24`                                        |
| `K`              | K               | Hour in am/pm (0-11)                             | `0`                                         |
| `h`              | h               | Hour in am/pm (1-12)                             | `12`                                        |
| `m`              | m               | Minute in hour                                   | `30`                                        |
| `s`              | s               | Second in minute                                 | `55`                                        |
| `S`              | S               | Millisecond                                      | `978`                                       |
| `z`              | z               | Time zone                                        | `Pacific Standard Time`; `PST`; `GMT-08:00` |
| `Z`              |                 | Time zone                                        | `-0800`                                     |
| `X`              |                 | Time zone                                        | `-08`; `-0800`; `-08:00`                    |
| '                | escape for text | Delimiter                                        | (none)                                      |
| '                | single quote    | Literal                                          | '                                           |

## docker-compose.yml

```yml
version: "2"
services:
  spark-worker-1:
    image: bde2020/spark-worker:2.3.2-hadoop2.7
    environment:
      SPARK_MASTER: spark://spark-master:7077
    ports:
      - 8081:8081/tcp
    labels:
      io.rancher.scheduler.affinity:host_label: spark-worker=true
  spark-master:
    image: bde2020/spark-master:2.3.2-hadoop2.7
    environment:
      INIT_DAEMON_STEP: setup_spark
    ports:
      - 8080:8080/tcp
      - 7077:7077/tcp
```

## rancher-compose.yml

```yml
version: "2"
services:
  spark-worker-1:
    scale: 1
    start_on_create: true
  spark-master:
    scale: 1
    start_on_create: true
```

## error

### java.lang.UnsupportedOperationException: parquet.column.values.dictionary.PlainValuesDictionary$PlainIntegerDictionary

Cause: schema in parquet files doesn't match hive table schema.
