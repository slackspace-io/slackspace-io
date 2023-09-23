Title: Learnings from Logstash
Date: 2014-07-06 19:00
Author: Jacob Morgan
Category: Operations
Tags: logstash, elasticsearch
Slug: logstash-learnings

I've been a user of splunk before, and can speak first hand the value of being able to visualize large sets of data. You simply can identify issues/patterns/stuff that you'd never otherwise see. I've started working with logstash at my new company, it's been a great experience. I am notorious for skimming docs, and experimenting to figure things out..So Here are some various things i've learned/bashed my head against on the way. I am sure many, if not all, are explained in the docs :)

Usefool tools:

*  [Grok Debugger](http://grokdebug.herokuapp.com/) -- Simply amazing. Once you get the hang of logstash/filters, it makes building new filters a breeze.
*  \#logstash @ freenode - Friendly and helpful
*  [Logstash.net](http://logstash.net) and [elasticsearch.org](http://elasticsearch.org) have top notch documentation.

Logstash leverages grok which has many prebuilt patterns that make standardized logs a breeze to import. I have a great deal of custom logs, which left me with building(and still much left) customized grok patterns. A few tips:

Start off with the raw logs going into logstash. Find the field you'd like to build a pattern for to better tag/break down. i.e. 'message' field. Take that field to the grok debugger and build your pattern. Once you have your pattern build your config.

    :::bash
    input {
      syslog {
        type => syslog
        port => 2514
      }
    }
    
    filter {
    
      if [type] == "syslog" {
        grok {
          break_on_match => "true"
          match => [ "message", "%{TIMESTAMP_ISO8601:timestamp} %{GREEDYDATA:junk} took '%{NUMBER:response_time:int}' ms%{GREEDYDATA:junk}Stuff Made = %{NUMBER:stuff_made:int}%{GREEDYDATA:junk}Things made = %{NUMBER:things_made:int}%{GREEDYDATA:junk}"]
          add_tag => [ "stuff", "things" ]
          tag_on_failure => []
        }
      }
    }


Pretty simple config, things I bashed my head against:

*  Building a grok for the entire log entry as it came from syslog. The match line is only matching against the 'message', not the entire line. Logstash is smart enough to get the timestamp, logsource, program name, etc and leaves the rest of it was 'message'. 
*  All entries were receiving '_grokparsefailure' tag, even when they also got the tags I set. I believe this is due to me attempting to match lines on filters that fail(hence the tag). The end goal is to only apply a filter to a line I expect to match, but until then using 'tag_on_failure = []' will prevent this duplicate tag from being created.
*  'type' can be anything you want. 'syslog' is an input plugin, and 'message' is a field of data -- and in this case created automatically by using the syslog input plugin. 
*  Many tutorials use type => 'syslog' in the grok statement. This is deprecated, the above is the current method.
* If you want to graph on a number you tag in a log, you need to include the :int after the semantic name like above. Otherwise you will get a class exceptio when building the graph.

Handy commands to use with ElasticSearch:

    :::bash
    curl -XDELETE 'http://localhost:9200/dopeysIndex*/'

I initially set logstash up, configured the syslog plugin and pumped everything into it -- a few months ago. The result? A *ton* of logs, but not very usable. I could search over a large set but I couldn't graph response time, error codes, or anything of real use. It was a very good method of consuming large amounts of disk space though:). The above command lets you delete indexes. Either rotate older indexes or wipe out everything. Be careful though, kibana-int holds your dashboards and you can just as easily delete it ;).  I wrote another page on using snapshots to backup data in general, or specific indexes such as kibana-int [here](/elasticsearch-snapshot.html) . 

 
Queries in Kibana:

*  [Lucene query article from ElasticSearch](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html#query-string-syntax) 
*  You can do things like \_exists\_:$tag, or tag:(query1 query2). This lets you create graphs that may cross multiple tags, but still limit data how you want.  This also goes back to how you build the dataset, which is something I am still going back and forth on. 
* AND, OR, NOT are how you string queries together. All of this is in the above link, but I know myself and other colleagues felt a bit unimpressed by kibana's search initially until we learned the Lucene query language. Much better now.

Things I am still wrestling with:

*  How to layout logstash.conf. With the amount of custom logs I am parsing it can get messy in a hurry.
*  Best use of tags/fields. By role, vendor, both? Many queries may cross roles/vendors but I'd still want to filter down when I can to improve performance.
*  How to deal with logs I am not tagging. Store to a different index I rotate more frequent and store tagged logs only in another? Discard them instantly?
*  Whether to use logstash-forwarder or not, currently not

