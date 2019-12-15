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

For this tutorial we will be using AWS Kinesis as our source for our stream data, so head over to the AWS console and create a new Kinesis Data Stream. Since this is a __Hello World!__ application, you can just give your stream 1 or 2 shards.

[Creating a Kinesis Data Stream](https://docs.aws.amazon.com/streams/latest/dev/amazon-kinesis-streams.html)
