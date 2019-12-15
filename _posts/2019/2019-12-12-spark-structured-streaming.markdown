---
layout: post
title: Getting Started with Apache Spark's Structured Streaming + AWS Kinesis
date: '2019-12-13 11:12'
excerpt: Using PySpark with AWS Kinesis Data Streams
comments: true
published: true
---
If you're not familiar with Apache Spark, here's a pretty good [blog post](https://mapr.com/blog/spark-101-what-it-what-it-does-and-why-it-matters/) that goes over what Spark is and it's popular use cases. Please note that this is a post on using Spark's **Structured Streaming** library, not Spark Streaming. If you need to get caught up to speed on the difference between the two, here are some links I found helpful.

[Spark Streaming vs. Structured Streaming](https://dzone.com/articles/spark-streaming-vs-structured-streaming) | 
[Spark: RDD vs Dataframes](https://blog.knoldus.com/spark-rdd-vs-dataframes/)

TL;DR  
The key difference between Spark Streaming and Structured Streaming is the API that each library provides. Spark Streaming provides us with the **DStream API**, where users are working with RDDs, while Structured Streaming is based on the **Dataframe/Dataset API** where users can use query language to manipulate their data.

For this tutorial we will be using AWS Kinesis as our source for our stream data, so head over to the AWS console and create a new Kinesis Data Stream. Since this is a _Hello World!_ application, you can just give your kinesis stream 1 or 2 shards to start with.

[Creating a Kinesis Data Stream](https://docs.aws.amazon.com/streams/latest/dev/amazon-kinesis-streams.html)

After your stream is created, you'll need to add data to it by using the following command in your terminal:

```terminal
aws kinesis put-records \
    --stream-name <your_stream_name> \
    --records Data=<>,PartitionKey=<>
```  
Or refer to the [AWS docs](https://docs.aws.amazon.com/cli/latest/reference/kinesis/put-records.html).

If your data load was success, you should see the following output:

```json
{
    "FailedRecordCount": 0,
    "Records": [
        {
            "SequenceNumber": "49602269145774455244746315270154389173555084512406274050",
            "ShardId": "shardId-000000000000"
        }
    ]
}
```

Now, open a new python file in any IDE or text editor you're comfortable with and lets start by creating a new _SparkSession_.

```python
from pyspark.sql import SparkSession

spark = SparkSession.builder \
    .appName("kinesis-test_app") \
    .config("spark.jars", "/path/to/jar/spark-sql-kinesis_2.11-2.4.0.jar") \
    .config("spark.jars.packages", "org.apache.spark:spark-streaming-kinesis-asl_2.11:2.4.4") \
    .getOrCreate()
```  

- _**SparkSession**_ is the front door to Spark's functionality and underlying APIs.  
- Provide Spark with your own _**appName**_, which does not have to be the same as the name of your Kinesis stream.
- The configuration properties are important things to make note of because these are external resources that are necessary to connect your PySpark application to Kinesis. Here is the download link to the <a href="https://github.com/mattwith2tees/pyspark_kinesis/blob/master/spark-sql-kinesis_2.11-2.4.0.jar" download="spark-sql-kinesis">jar file</a>. The next package comes from the Maven Repository so you can paste as is. Both packages are necessary to integrate Spark with Kinesis.
- Finally, we instantiate the SparkSession with the _getOrCreate()_ method.

