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

For this tutorial I will be using AWS Kinesis as my source for our stream data, so head over to the AWS console and create a new Kinesis Data Stream. Since this is a _Hello World!_ application, you can just give your kinesis stream 1 or 2 shards to start with.

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
- The configuration properties are important things to make note of because these are external resources that are necessary to connect your PySpark application to Kinesis. Here is the download link to the <a href="https://github.com/mattwith2tees/pyspark_kinesis/blob/master/spark-sql-kinesis_2.11-2.4.0.jar" download="spark-sql-kinesis">jar file</a>. The next package comes from the Maven Repository so you can paste as is. Remember that both packages are necessary to integrate Spark with Kinesis.
- Finally, I instantiate the SparkSession with the _**getOrCreate()**_ method.

Now that the session is up and running, I can create a Dataframe to read the stream data:

```python
kinesisDF = spark.readStream \
    .format('kinesis') \
    .option('streamName', '<aws_kinesis_stream_name>') \
    .option('region', '<your_region>') \
    .option('endpointUrl', 'https://kinesis.us-east-1.amazonaws.com/') \
    .option('startingPosition', 'earliest') \
    .option('awsAccessKeyId', '<your_access_key>') \
    .option('awsSecretKey', '<your_secret_key>') \
    .load()
```
 
Here is where I begin to read the data stream, hence the _**readStream**_ property.
- I specify the _format()_ as Kinesis, because that is the source of our data stream.
- Provide the name of your Kinesis stream in AWS.
- Prove the region.
- The _**endpointUrl**_ is just your regional endpoint. If your region is us-east-1, then your endpointUrl will be exactly like the one above.
- _**startingPosition**_ is where you want to begin fetching data from in Kinesis. The possible values are: "latest", "trim_horizon", "earliest"
- Lastly, provide spark with your AWS Access Key and Secret Key, then _**load()**_.

Once your dataframe is created, now I can use Spark SQL to query the dataframe and evaluate our data:

```python
query = kinesisSpark \
    .selectExpr('CAST (data as STRING)').alias("stream_data") \
    .writeStream \
    .format('console') \
    .option('truncate', 'false') \
    .start() \
    .awaitTermination()
```

- _**selectExpr**_ is basically a _select_ statement, but with the ability to consume SQL expressions. Furthermore, I cast _data_ as String, because if I'm not mistaken, incoming stream data comes in as binary data.
- I execute the query by calling the _**writeStream**_ property.
- _**format()**_ determines where the SQL output ends up. In this case, I want to receive the output in my console.
- _**option('truncate', 'false')**_ just tells Spark that I do not wish to have my columns truncated.
- Finally I _**start()**_ the query, and I include _**awaitTermination()**_ so that Spark will wait on the termination signal from me.

When you run the application (I'm running in PyCharm), you should see the batch runs and stages:

![Screen Shot 2019-12-15 at 2.18.31 AM.png]({{site.baseurl}}/img/Screen Shot 2019-12-15 at 2.18.31 AM.png)

If you're not using PyCharm and running from your terminal, use the following command:

```
spark-submit --jars spark-sql-kinesis_2.11-2.4.0.jar --packages org.apache.spark:spark-streaming-kinesis-asl_2.11:2.4.4 <your_python_file>.py
```

If you don't see any details about your Kinesis stream in the console, here are some possible problems.

- If you are receiving an error that says, "Unable to load credentials", check your AWS credentials in your configuration properties. Make sure that you're using the right Access and Secret Keys. Also, make sure that you check the spelling in your file:

```python
.option('awsAccessKeyId', '<your_access_key>') \
.option('awsSecretKey', '<your_secret_key>') \
```
I kept receiving this error, because I left out the _Id_ in _awsAccessKeyId_. 🤦🏾‍♂️

- If you're getting an error that says, "Failed to find data source: kinesis", that means your jar file did not download correctly, or at all. Make sure you're using the absolute path when pointing to your jar file.

If you're getting other errors and still cannot seem to figure it out, please leave a comment and I will be more that happy to help you out.

For the ones who were successful, congratulations and happy coding!


