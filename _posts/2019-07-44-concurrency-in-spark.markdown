---
layout: post
title:  "Concurrency in Spark"
date:   2019-07-24 20:07:50 +0800
---

Over the past few days I've run into some issues with porting a Scala/Apache Spark library
from Databricks to AWS Glue. I thought I'd do a writeup, and maybe stop some other data
engineers from stumbling down the same rabbit hole I fell into.
# The Problem
When executing spark streams on AWS Glue, sometimes they will just be stopped out of nowhere for no apparent reason.
This does not happen to identical code on Databricks.
# The Reason
To submit a job on AWS, a Scala object is created, which is run directly with spark-submit.
This means as soon as your main function exits, the spark runtime is shut down.
The problem with this is that spark streaming is asynchronous: when you tell spark to write a stream somewhere, it doesn’t block the main thread. Instead it writes the stream in the background, letting the rest of your code keep running.
In performance critical environments this is a good thing: If a stream is continuously being written non-stop, it allows other actions like logging to occur at the same time.
However, problems arise when you treat the write functions as executing immediately. In this case, the main function exits before all the data has been written, causing cryptic error messages and much confusion.
The reason this doesn’t happen on Databricks is that the spark runtime continues to stay alive after your code has finished, and makes sure everything has finished before shutting down.
# The Fix
To deal with this, spark provides a StreamingQuery class, which is returned from a ```.writeStream.start()``` call. Through this class, the stream can be managed with functions like ```processAllAvailable()``` (Blocks the main thread until all available data has been written) or ```stop()``` (Stops execution of the stream, blocking until it is safe to do so).
# The Code
When writing to multiple locations at once, blocking the main thread using the functions above is not recommended.

{% highlight scala %}
val query1 = exampleData.writeStream.start(<path>)
val query2 = exampleData.writeStream.start(<path>)
query1.processAllAvailable()
query1.stop()
query2.processAllAvailable()
query2.stop()
{% endhighlight %}

The above code is bad practice, as it forces spark to write to only one location at a time.
The proper way to do this is to use Scala’s threading capabilities.

{% highlight scala %}
import ExecutionContext.Implicits.global

val f1: Future[Unit] = Future {
  val query1 = exampleData.writeStream.start(<path>)
  query1.processAllAvailable()
  query1.stop()
}

val f2: Future[Unit] = Future {
  val query2 = exampleData.writeStream.start(<path>)
  query2.processAllAvailable()
  query2.stop()
}
{% endhighlight %}

To execute these futures at the same time, we can use a function such as Zip or FoldRight to combine them, and then await their results.
However, it is worth keeping in mind that if one fails, the ‘zipped’ future will also fail.
To circumvent this, errors must be handled before zipping.

{% highlight scala %}
val handleWriteFailure = (x: Future[Unit]) => (x recover {case err => log.error(err.toString())}) : Future[Unit]
val zippedFuture = handleWriteFailure(f1) zip handleWriteFailure(f2)
Await.result(zippedFuture, Duration.Inf)
{% endhighlight %}
