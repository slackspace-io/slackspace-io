Title: Purging Varnish 
Date: 2014-07-06 14:00
Author: Jacob Morgan
Category: Operations
Tags: pelican, bash, varnish
Slug: varnishPurge
Using Varnish means content is cached. A pretty essential requirement is to have the ability to purge(or ban in Varnish 3) content on demand. 

There is a couple ways to do this, you can modify your vcl to have such functions so you can do a PURGE request with the desired URL..Or you can use varnishadm. I am using varnish 3+, which means the purge command has been replaced with ban. Here is the varnishadm command to purge/ban all documents under http://dopey.io/drafts/*

    :::bash
    varnishadm -S /etc/varnish/secret -T localhost:6082 ban.url drafts/*

I was going to limit purge request to localhost anyway, so it seemed simpliest to use varnishadm for my requirements. I wrote a little script to aid in publishing new post. It will find any changes in my content then purge those pages automatically. It will also purge all drafts, categories, tags and the index page.



    :::bash
    #!/bin/sh
    cd /var/www/html/dopey/
    mkdir -p /tmp/dopey
    make html
    md5sum /var/www/html/dopey/content/* 2>/dev/null > /tmp/dopey/content.new
    while read line; do
      grep "^$line$" /tmp/dopey/content &>/dev/null || {
        page=$(echo $line | awk '{print $2}')
        slug=$(grep "^Slug:" $page | awk -F: '{print $2}' | tr -d ' ')
        echo "Purged http://dopey.io/$slug.html"
        varnishadm -S /etc/varnish/secret -T localhost:6082 ban.url $slug.html &>/dev/null
      }
    done < /tmp/dopey/content.new
    varnishadm -S /etc/varnish/secret -T localhost:6082 ban.url /category/* &>/dev/null
    varnishadm -S /etc/varnish/secret -T localhost:6082 ban.url /tags/* &>/dev/null
    varnishadm -S /etc/varnish/secret -T localhost:6082 ban.url / &>/dev/null
    varnishadm -S /etc/varnish/secret -T localhost:6082 ban.url drafts/* &>/dev/null
    mv /tmp/dopey/content.new /tmp/dopey/content


