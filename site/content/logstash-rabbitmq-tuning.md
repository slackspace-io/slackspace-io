Title: RabbitMQ and Logstash
Date: 2014-09-14 15:00
Author: Jacob Morgan
Category: Operations
Tags: logstash, rabbitmq
Slug: logstash-rabbitmq-tuning


My current setup is a pretty common Logstash stack. It consist of various inputs coming into a non filtering Logstash which dumps these messages into RabbitMQ. From RabbitMQ I can have multiple Logstash indexers slurp from RabbitMQ, apply filtering, and output to Elasticsearch, on to further processing, etc.

The only articles I found when attempting this referenced the AMPQ input/output plugins, which is not how one integrates with RabbitMQ these days. As well, there wasn't much information around tuning. Here is my current configuration along with some comments of various settings I tried/results I experienced.


The flow:

*Log/Data inputs -> LS(No filters) -> RabbitMQ -> LS Indexers(filtering) -> Elasticsearch/Other.*

RabbitMQ output configuration:

    :::code
    rabbitmq {
        exchange => "logstash-rabbitmq"
        exchange_type => "direct"
        key => "logstash-key"
        host => ""
        workers => 4
        durable => true
        persistent => true
    }


RabbitMQ input configuration:

    :::code
    rabbitmq {
        host => ""
        queue => "logstash-queue"
        durable => true
        key => "logstash-key"
        exchange => "logstash-rabbitmq"
        threads => 3
        prefetch_count => 50
        port => 5672
        user => ""
        password => ""
    }

Key values:

*    **exchange** - Match on output and input plugin
*    **key** - Match on output and input plugin
*    **queue** - Must be set on input plugin, it should also match on all indexers.  Otherwise, a 'random' queue is created each time LS connects. 
*    **durable** - Set to true so queue persist through a crash or restart of RabbitMQ.  
*    **persistent** - Set to true so messages are written to disk and persist through RabbitMQ restarting.



Initially I had a hard time achieving more than 1500msg/sec on the indexer side. This would result in the queue to fill up indicating my Logstash indexers had fallen behind. 

RabbitMQ Tuning
---------------

At first it would appear the bottleneck is on the RabbitMQ server. Checking iotop one would find *allot* of disk I/O. RabbitMQ has some great writeups about why and when it writes to disk [here](http://www.rabbitmq.com/blog/2011/01/20/rabbitmq-backing-stores-databases-and-disks/). In short, since message acknowledgement is enabled every message is written to disk. This does not mean every message is read from disk, which you can observe from iotop. Lots of writing, no reading. RabbitMQ will eventually start reading from disk if the queue gets large enough and RabbitMQ consumes enough of system memory. I added the following configuration to discourage RabbitMQ from this behaviour:

    :::code
    {vm_memory_high_watermark, 0.8},
    {vm_memory_high_watermark_paging_ratio, 0.8}

This is the only configuration I've done thus far with RabbitMQ, and after the Logstash tuning below it may not even be needed. RabbitMQ is fast, and I don't expect it to be my bottleneck anytime soon.


Logstash Tuning 
---------------

The setting *no_ack => true* greatly improved the throughput, or so it appeared at first.  By default Logstash sends an acknowledgement after each message is processed. Turning off this setting means RabbitMQ will not wait for the 'ack' and continue sending messages to Logstash. This resulted in the queue being empty even under the highest load, and what appeared to be better throughput. Seeing 7-9k/sec events yet nothing in the Queue. Unfortunately this was just an illusion.

Logstash uses a 'SizedQueue' hardcoded to 20 to throttle messages from input -> filter. The intent is if filters are busy this will block inputs, which in theory would stop receiving new events until there is free resources.

This works with *no_ack=>false* because RabbitMQ plugin sends an ack *after* it puts the event in the SizedQueue. If the SizedQueue is full it blocks until there is space, which blocks the ack. This in turns prevents more events coming from RabbitMQ, and thus the Queue on RabbitMQ begins filling up.

With *no_ack*=>true RabbitMQ ignores the prefetch count and sends all of the events in the queue straight into LogStash.  Once the SizedQueue is fulll the events continue to pile up behind the ones waiting for resources. Memory consumption grows and eventually OOM Exceptions are thrown. On top of this, you lose every message that had been sent from RabbitMQ -> Logstash since *no_ack=>true* means RabbitMQ forgets about the message the moment it sends it. You also lose messages every time you restart an indexer with this configuration. 

Sending the ack very well may be a performance decrease, though I don't know how much of one,  but it certainly is not worth the risk of losing messages.

The way I've been able to increase throughput is by increasing the number of filter workers Logstash uses. According to this [Jira issue](https://logstash.jira.com/browse/LOGSTASH-2161) setting worker threads in sysconfig do not work, so one must use '--filterworkers'. You can set this in LS_OPTS value, or however you wish to pass it at startup. This allows faster processing of events from input->filter thus preventing RabbitMQ from blocking when waiting for a spot in the queue. Unfortunately this limits you to using on filters that are threadsafe. 




With these settings we've reached 6-9k/sec per indexer at peak with no hesitation. This performance is greatly dependent on your filters, which we have a considerable amount of.  


