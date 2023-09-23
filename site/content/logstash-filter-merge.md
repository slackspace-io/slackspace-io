Title: Merge Filter for Logstash
Date: 2014-08-01 17:00
Author: Jacob Morgan
Category: Operations
Tags: logstash, elasticsearch, ruby
Slug: logstash-filter-merge

The merge filter let's you combine two events that occur within a period into a new single event. This lets you graph/analyze the combination of certain events. The filter is available on github [here](https://github.com/jpyth/logstash-contrib/blob/master/lib/logstash/filters/merge.rb)

Options:

*  key => Unique identifier, used to match the two events you want to merge.
*  order => 'first' or 'last', the order the events should arrive 
*  merge_tag => Tag(s) to add on the *new* event. 
*  period => Max length of time between events(seconds). 

There is an existing bug in Logstash that impacts filters which create new events. The github issue is [here](https://github.com/elasticsearch/logstash/issues/1548) . This bug causes the new event to hit *every* filter below it in the config, regardless of the conditionals. This can result in additional tags being added, or other filters being applied to the event if they match. Not to mention a performance hit. 

Fortuantely there is a work around, albeit crude/cumbersome. The deprecated 'tags => ' and 'type =>' conditionals within a filter statement still work. My current method of minimizing impact from this bug is a three step process.

*  Write the conditional i'd normally use for the merge filter, but instead mutate and add a unique tag to that event. This can be done anywhere in the config.
*  At the very bottom of my filter configuration list the merges out, using the 'tags =>' option with the unique tag added above
*  Use Mutate at after the merges to remove these tags so they do not end up in ElasticSearch.

In my setup I also group all my merges in a single conditional at the bottom of the config to limit the number of events that then are compared against the 'tags =>' conditional. If you have any filters/conditionals without a 'tags =>' option after your merges the event will be ran through it. This same issue applies to the metric, alert, clone, and any other event that creates new Logstash events.




Here is an example configuration doing the above. 

    :::code
    input {
      file {
        type => example
        path => "/tmp/exampleData.log"
      }
    }
    
    filter {
    
    #work around for event bug, add tags to events I want to track.
      if [type] == "example" {
        if "a" == [message] {
          mutate {
            add_tag => "A"
          }
        }
        if "b" == [message] {
          mutate {
            add_tag => "B"
          }
        }
    
    ###Other filters/configuration ### 

    #merges
        merge {
          tags => "A"
          key => "key-A"
          order => "first"
          merge_tag => "mergeTagA"
          period => 5 
        }
        merge {
          tags => "B"
          key => "key-A"
          merge_tag => "mergeTagB"
          order => "last"
        }
      }

    #Remove the temp tags    
      mutate {
        remove_tag => ["A","B"] 
      }
    }
    
    output {
      stdout {
        codec => rubydebug
      }
    }
    

Sending this data:

    :::code
    [dopey@dopey ~]# cat a
    a
    a
    b
    c
    d
    [dopey@dopey ~]# 

Here is the result:

    :::code
    {
           "message" => "a",
          "@version" => "1",
        "@timestamp" => "2014-08-01T13:45:36.729Z",
              "type" => "example",
              "host" => "dopey.io",
              "path" => "/tmp/exampleData.log",
              "tags" => []
    }
    {
           "message" => "a",
          "@version" => "1",
        "@timestamp" => "2014-08-01T13:45:36.747Z",
              "type" => "example",
              "host" => "dopey.io",
              "path" => "/tmp/exampleData.log",
              "tags" => []
    }
    {
           "message" => "b",
          "@version" => "1",
        "@timestamp" => "2014-08-01T13:45:36.750Z",
              "type" => "example",
              "host" => "dopey.io",
              "path" => "/tmp/exampleData.log",
              "tags" => []
    }
    {
           "message" => "c",
          "@version" => "1",
        "@timestamp" => "2014-08-01T13:45:36.753Z",
              "type" => "example",
              "host" => "dopey.io",
              "path" => "/tmp/exampleData.log"
    }
    {
           "message" => "d",
          "@version" => "1",
        "@timestamp" => "2014-08-01T13:45:36.758Z",
              "type" => "example",
              "host" => "dopey.io",
              "path" => "/tmp/exampleData.log"
    }
    #New merged event below.
    {
                "@version" => "1",
              "@timestamp" => "2014-08-01T13:45:36.750Z",
                  "source" => "dopey.io",
               "merge.key" => "key-A",
        "merge.time_delta" => 0.003,
                 "message" => "a",
                    "type" => "example",
                    "host" => "dopey.io",
                    "path" => "/tmp/exampleData.log",
                    "tags" => [
            [0] "mergeTagA",
            [1] "mergeTagB"
        ],
               "message-2" => "b"
    }
  

Some future improvements before I submit it to the logstash-contrib:

*  Allow more than 2 events to be tracked/merged. Working on how i'd configure/manage this.
*  Prevent 'add_tag' from being added to the *new* event -- only merge_tag. If I tried to prevent this behaviour, the results were unpredictable. For now, if you have add_tag on the merge filter the tag will be added to both the original trigger event, and the new event. merge_tag will only be added on the new merged event.
*  Evaluate time on incoming events before overriding existing value. Right now, if your events come out of order the last one seen by logstash will be stored. 


I've used this for a variety of use cases so far. It allows me to see how often action A followed by B happened, or A followed by C, etc. Hopefully others find a need as well. 

