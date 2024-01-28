# type

- https://arrow.apache.org/docs/python/api/datatypes.html
- https://cwiki.apache.org/confluence/display/hive/languagemanual+types
- https://spark.apache.org/docs/latest/api/java/org/apache/spark/sql/types/DataType.html
- https://clickhouse.com/docs/en/sql-reference/data-types/int-uint

| ros      | clickhouse | pyarrow parquet                                          | hive data type                          | spark sql type            | spark default size in bytes      | scala                                  |
| -------- | ---------- | -------------------------------------------------------- | --------------------------------------- | ------------------------- | -------------------------------- | -------------------------------------- |
|          |            |                                                          |                                         | Null                      |                                  | Option[]=None                          |
|          |            |                                                          | BINARY                                  | BinaryType                | 100                              | Array[Byte]                            |
| byte     | Int8       | pa.int8()                                                | TINYINT                                 | ByteType                  | 1                                | Byte                                   |
| int8     | Int8       | pa.int8()                                                | TINYINT                                 | ByteType                  | 1                                | Byte                                   |
| bool     | UInt8      | pa.bool\_()                                              | BOOLEAN                                 | BooleanType               | 1                                | Boolean                                |
| uint8    | UInt8      | pa.uint8()                                               | SMALLINT                                | ShortType (extended)      | 2                                | Short (extended)                       |
| int16    | Int16      | pa.int16()                                               | SMALLINT                                | ShortType                 | 2                                | Short                                  |
| uint16   | UInt16     | pa.uint16()                                              | INT                                     | IntegerType (extended)    | 4                                | Int (extended)                         |
| int32    | Int32      | pa.int32()                                               | INT                                     | IntegerType               | 4                                | Int                                    |
| uint32   | UInt32     | pa.uint32()                                              | BIGINT                                  | LongType (extended)       | 8                                | Long (extended)                        |
| int64    | Int64      | pa.int64()                                               | BIGINT                                  | LongType                  | 8                                | Long                                   |
| uint64   | UInt64     | pa.uint64()                                              | DECIMAL                                 | DecimalType (use decimal) | 8 / 16                           | java.math.BigInt / BigDecimal          |
| float32  | Float32    | pa.float32()                                             | FLOAT                                   | FloatType                 | 4                                | Float                                  |
| float64  | Float64    | pa.float64()                                             | DOUBLE                                  | DoubleType                | 8                                | Double                                 |
| string   | String     | pa.string()                                              | STRING                                  | StringType                | 20                               | String                                 |
|          |            |                                                          | VARCHAR                                 | VarcharType               |                                  | String                                 |
|          |            |                                                          | CHAR                                    | CharType                  | 16                               | char                                   |
|          | Array(T)   |                                                          | ARRAY                                   | ArrayType                 | 100 \* element type default size | scala.collection.seq / List            |
|          |            |                                                          | STRUCT                                  | StructType                | all fields default sizes total   | org.apache.spark.sql.Row               |
| time     |            | pa.struct([("secs", pa.int64()), ('nsecs', pa.int64())]) | STRUCT<`secs`: BIGINT, `nsecs`: BIGINT> |                           |                                  |                                        |
| duration |            | pa.struct([("secs", pa.int64()), ('nsecs', pa.int64())]) | STRUCT<`secs`: BIGINT, `nsecs`: BIGINT> |                           |                                  |                                        |
|          | DateTime   |                                                          | TIMESTAMP                               | TimestampType             |                                  | java.sql.Timestamp / java.time.Instant |
|          |            |                                                          | DATE                                    | DateType                  |                                  | java.sql.Date                          |
|          |            |                                                          | MAP                                     | MapType                   |                                  | scala.collection.Map                   |
