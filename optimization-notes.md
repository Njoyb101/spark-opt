## 1. Dynamic Allocation and Resource Management

Dynamic allocation ensures efficient resource usage based on workload requirements.

```bash
# Enable dynamic allocation
spark.dynamicAllocation.enabled=true

# Set minimum and maximum number of executors
spark.dynamicAllocation.minExecutors=1
spark.dynamicAllocation.maxExecutors=10
```
## 2. Partitioning and Shuffle Optimization
```python
print("Number of partitions:", df.rdd.getNumPartitions())

df.repartition(10).write.format("parquet").save("path")
```
Set Shuffle Partitions: Optimize based on data size.
```bash
spark.sql.shuffle.partitions=200
```
## 3. S3 Writes Optimization
For writing data to S3, configure the S3A Magic Committer.

```scala
val spark: SparkSession = SparkSession.builder()
    .config("spark.serializer", "org.apache.spark.serializer.KryoSerializer")
    .config("spark.hadoop.fs.s3a.impl", "org.apache.hadoop.fs.s3a.S3AFileSystem")
    .config("spark.hadoop.fs.s3a.committer.magic.enabled", "true")
    .config("fs.s3a.committer.name", "magic")
    .getOrCreate()
```
Limit records per file
```python
df.write.option("maxRecordsPerFile", 1000000).save("path")
```
## 4. Join and Skew Optimization

```python
# broadcast
df_large.join(broadcast(df_small), "key")

#skew hints on databricks only
df.join(df_skew.hint("SKEW", "skewKey", "skewedKeys"), ["key"], "inner")

# Make the default broadcast threshold as per your needs  default is 10Mb
spark = SparkSession.builder \
    .appName("BroadcastThresholdExample") \
    .config("spark.sql.autoBroadcastJoinThreshold", 50 * 1024 * 1024) \  # 50MB in bytes
    .getOrCreate()
```
## 5. Serialization with Kryo

```bash
spark.serializer=org.apache.spark.serializer.KryoSerializer
spark.kryo.registrator=MyKryoRegistrator
spark.kryoserializer.buffer.max=512m
```
## 6. Disk I/O Optimization
```bash
spark.shuffle.file.buffer=64k # Increase Shuffle Buffer Size
spark.file.transferTo=false # Use in mem spill merging
```
## 7. Change Scheduling Type
```bash
spark.scheduler.mode=FAIR
```
