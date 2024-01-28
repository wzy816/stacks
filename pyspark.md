# pyspark

不支持 udaf，不支持多行

## install

```bash
# install pyspark via pip
pip install pyspark

#open
pyspark
```

## import

```python
import math
import pandas as pd
from datetime import datetime
from itertools import product

from pyspark.sql.window import Window
from pyspark.ml.feature import Bucketizer
from pyspark.sql.functions import col, udf
import pyspark.sql.functions as F
from pyspark.sql.types import StringType,ArrayType, DateType, IntegerType
```

## config

```python
from pyspark import SparkConf
from pyspark.sql import SparkSession

# set conf
conf = SparkConf()
conf.set("spark.executor.memory", "4g")
conf.set('fs.defaultFS', 'hdfs://hdfs')
conf.set('dfs.nameservices', 'hdfs')
conf.set('dfs.ha.namenodes.hdfs', 'name-0-node,name-1-node')
conf.set('dfs.namenode.http-address.hdfs.name-0-node', 'ip:9002')
conf.set('dfs.namenode.http-address.hdfs.name-1-node', 'ip:9002')
conf.set('dfs.client.failover.proxy.provider.hdfs', 'org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider')
spark = SparkSession.builder.appName('my_app').config(conf=conf).getOrCreate()

# read conf
confs = sc._conf.getAll()
confs.sort(key=lambda x:x[0])
print("\n".join(['%s = %s' % (kv[0],kv[1]) for kv in confs]))
```

## pyspark

## module

sc.addPyFile("hdfs:///user/a.zip")

### udf

```python
def my_func(text):
  return text
my_udf = udf(my_func, StringType())

spark.udf.register("my_udf", my_func, StringType())

@udf("double")
def avg(arr):
    if arr is None:
        return None
    l = [x for x in arr if x >= 0]
    return sum(l) / len(l)
```

### sql

```python
statement = """
select
  a,
  b
from db.table
where day > '2019040101'
  and item in ('item1', 'item2')
  and (module.field1 = 1 or module.field2 = 2)
  and field3 is not null
order by column1
"""

statement = """
SELECT a
FROM db.table
WHERE day >= '{start_date}' and day <= '{end_date}'
""".format(start_date=start_date, end_date=end_date)

df = spark.sql(statement).persist()
```

### filter

```python
df.filter(df.column1=="value1")
df.filter("column1=='value1'")
df.filter(df.column1.isNotNull())
lines.filter(lambda line: "Python" in line)
df.filter((df.column1==2)&(df.column2>0.05)&(df.column1-df.column2<0.1))
df.filter(df.soc.isNotNull())
```

### withColumn

```python
df = df.withColumn('new_col',my_udf(col('column1'),col('column2'), lit(variable)))
df = df.withColumnRenamed('count(total)', 'total_count_1h')
df = df.withColumn('new_col', when((df.column1 <= df.column2) & (df.column2 <= df.column3),1).otherwise(0))
df = df.withColumn('new_col',F.when(df.column1.isin (1,2),1).when(df.column2.isin (1,2),0))
df.withColumn('column1',psf.explode('column1.list_field'))
```

### groupby

```python
df = df.groupby(df.column1, df.column2).agg({'column3': 'count'})
df = df.groupBy('column1','column2').agg(F.collect_set('column3'))
df = df.groupBy('column1','column2').agg(F.min(df.column3),F.sum(df.column3),F.count(df.column3))
```

### window

```python
win = Window.partitionBy("column1", "column2").orderBy("column3")
```

### union

```python
df_list=df_list1.union(df_list2).union(df_list3)
```

### join

```python
df = df1.join(df2,['column1','column2'],'left').select(df1["*"],df2.column3)
```

### view

```python
df_list.createOrReplaceTempView("list_view")
```

### read

```python
df = spark.read.option("header","true").csv("df_list")
df = spark.read.csv('hdfs://ip:9001/my_file.csv', inferSchema=True, header=True)
df = spark.read.parquet("_.parquet")
df = spark.read.csv("_.csv")
```

### write

```python
df.saveAsTextFile()
df.saveAsSequenceFile()
df.write.option("header", True).csv('/temp/')
df.write.mode("overwrite").option("header", "true").csv("df_list")
df.write.mode('append').parquet('/temp/')
df.write.format("parquet").save('/temp/')
df.coalesce(1).write.mode("overwrite").option("header","true").csv("df_list")
df.write.format('json').mode('overwrite').save("df_list")
```

### distinct

```python
keys = data.select('key').distinct().collect()
```

### fill

```python
df.na.fill(0.0)
```

### drop

```python
df = df.drop('column1','column2')
```

### partition

```python
df.rdd.getNumPartitions()
```
