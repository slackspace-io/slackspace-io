Title: Snapshots in ElasticSearch
Date: 2014-07-06 19:00
Author: Jacob Morgan
Category: Operations
Tags: logstash, elasticsearch
Slug: elasticsearch-snapshot

I'm in the process of building out a logstash setup at work. We have a considerable amount of logs from a number of sources. One thing I wanted to figure out was how to backup/restore the various dashboards I am making along the way. Doing them one at a time from Kibana seemed inefficient. ElasticSearch snapshot feature is the answer. In general it's to backup/restore any/all of your indices. Your dashboards are stored in kibana-int index.


The steps are listed out here [Here](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/modules-snapshots.html). Here is how it went for me. There isn't much more here than what is on that page, their instructions are quite good:) 

Create the local repo:

    :::bash
    curl -XPUT 'http://localhost:9200/_snapshot/dopey_backup' -d '{
        "type": "fs",
        "settings": {
            "compress" : true,
            "location": "/var/data/elasticsearch/backup"
        }
    }'

The directory used for backups needs to be writable by the elasticsearch users:

    :::bash
    [dopey@dopey-logstash ~]#  curl -XPUT "localhost:9200/_snapshot/dopey_backup/snapshot_1?wait_for_completion=false"
    {"error":"SnapshotCreationException[[dopey_backup:snapshot_1] failed to create snapshot]; nested: FileNotFoundException[/var/data/elasticsearch/backup/snapshot-snapshot_1 (Permission denied)]; ","status":500}
    [dopey@dopey-logstash ~]# chown -R elasticsearch:elasticsearch /var/data/elasticsearch/backup/
    [dopey@dopey-logstash ~]#  curl -XPUT "localhost:9200/_snapshot/dopey_backup/snapshot_1?wait_for_completion=false"
    {"accepted":true}


You can see the backup being built in your set location:

    :::bash
    [dopey@dopey-logstash ~]# ls -lh /var/data/elasticsearch/backup/
    total 16K
    -rw-r--r--. 1 elasticsearch elasticsearch   31 Jul  2 13:27 index
    drwxr-xr-x. 5 elasticsearch elasticsearch 4.0K Jul  2 13:27 indices
    -rw-r--r--. 1 elasticsearch elasticsearch  522 Jul  2 13:27 metadata-snapshot_1
    -rw-r--r--. 1 elasticsearch elasticsearch  256 Jul  2 13:27 snapshot-snapshot_1
    [dopey@dopey-logstash ~]#

Check on status of the backups:

    :::bash
    [dopey@dopey-logstash ~]# curl -s -XGET "localhost:9200/_snapshot/dopey_backup/_all"| python2 -mjson.tool
    {
        "snapshots": [
            {
                "duration_in_millis": 7397, 
                "end_time": "2014-07-02T11:39:08.893Z", 
                "end_time_in_millis": 1404301148893, 
                "failures": [], 
                "indices": [
                    "logstash-2014.07.02", 
                    "kibana-int"
                ], 
                "shards": {
                    "failed": 0, 
                    "successful": 10, 
                    "total": 10
                }, 
                "snapshot": "snapshot_1", 
                "start_time": "2014-07-02T11:39:01.496Z", 
                "start_time_in_millis": 1404301141496, 
                "state": "SUCCESS"
            }
        ]
    }
    [dopey@dopey-logstash ~]# 

The kibana-int indice is what contains your dashboards. If I ever wanted to just restore those i'd do:

    :::bash
    curl -XPOST "localhost:9200/_snapshot/dopey_backup/snapshot_1/_restore" -d '{
        "indices": "kibana-int",
        "ignore_unavailable": "true",
        "include_global_state": false 
   
    }'


