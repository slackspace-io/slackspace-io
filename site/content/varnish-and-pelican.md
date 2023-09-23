Title: Varnish and Pelican Migration
Date: 2014-06-29 18:00
Author: Jacob Morgan
Category: Operations
Description: Varnish and Pelican migration fun!
Tags: varnish, pelican, fedora
Slug: varnish

I decided to move off wordpress. A couple former colleagues used Pelican and I've always wanted to try it. Also took this chance to configure varnish infront of all my sites. I've been over deployments of varnish, but this is my first time deploying it for personal use. I used my friend [Scott's](http://codetwit.com/varnish-setup.html) writeup on varnish as a base. There are a few differences on Fedora though, such as systemd being configured different than /etc/default method.

Let's go..

Install varnish, included in Fedora's Main repos

    :::bash
    [root@jmorgan dopey] yum install varnish
    Resolving Dependencies
    --> Running transaction check
    ---> Package varnish.x86_64 0:3.0.3-5.fc17 will be installed
    --> Processing Dependency: varnish-libs = 3.0.3-5.fc17 for package: varnish-3.0.3-5.fc17.x86_64
    --> Processing Dependency: libvarnishapi.so.1(LIBVARNISHAPI_1.0)(64bit) for package: varnish-3.0.3-5.fc17.x86_64
    --> Processing Dependency: jemalloc for package: varnish-3.0.3-5.fc17.x86_64
    --> Processing Dependency: libvgz.so()(64bit) for package: varnish-3.0.3-5.fc17.x86_64
    --> Processing Dependency: libvcl.so()(64bit) for package: varnish-3.0.3-5.fc17.x86_64
    --> Processing Dependency: libvarnishcompat.so()(64bit) for package: varnish-3.0.3-5.fc17.x86_64
    --> Processing Dependency: libvarnishapi.so.1()(64bit) for package: varnish-3.0.3-5.fc17.x86_64
    --> Processing Dependency: libvarnish.so()(64bit) for package: varnish-3.0.3-5.fc17.x86_64
    --> Processing Dependency: libjemalloc.so.1()(64bit) for package: varnish-3.0.3-5.fc17.x86_64
    --> Running transaction check
    ---> Package jemalloc.x86_64 0:3.4.0-1.fc17 will be installed
    ---> Package varnish-libs.x86_64 0:3.0.3-5.fc17 will be installed
    --> Finished Dependency Resolution
    
    Dependencies Resolved
    
    ==============================================================================================================================================================
     Package                                 Arch                              Version                                   Repository                          Size
    ==============================================================================================================================================================
    Installing:
     varnish                                 x86_64                            3.0.3-5.fc17                              updates                            344 k
    Installing for dependencies:
     jemalloc                                x86_64                            3.4.0-1.fc17                              updates                             97 k
     varnish-libs                            x86_64                            3.0.3-5.fc17                              updates                            154 k
    
    Transaction Summary
    ==============================================================================================================================================================
    Install  1 Package (+2 Dependent packages)
    
    Total download size: 596 k
    Installed size: 1.5 M
    Is this ok [y/N]: y
    Downloading Packages:
    (1/3): jemalloc-3.4.0-1.fc17.x86_64.rpm                                                                                                |  97 kB  00:00:00     
    (2/3): varnish-libs-3.0.3-5.fc17.x86_64.rpm                                                                                            | 154 kB  00:00:00     
    (3/3): varnish-3.0.3-5.fc17.x86_64.rpm                                                                                                 | 344 kB  00:00:01     
    --------------------------------------------------------------------------------------------------------------------------------------------------------------
    Total                                                                                                                         369 kB/s | 596 kB     00:01     
    Running Transaction Check
    Running Transaction Test
    Transaction Test Succeeded
    Running Transaction
      Installing : jemalloc-3.4.0-1.fc17.x86_64                                                                                                               1/3 
      Installing : varnish-libs-3.0.3-5.fc17.x86_64                                                                                                           2/3 
      Installing : varnish-3.0.3-5.fc17.x86_64                                                                                                                3/3 
      Verifying  : varnish-3.0.3-5.fc17.x86_64                                                                                                                1/3 
      Verifying  : varnish-libs-3.0.3-5.fc17.x86_64                                                                                                           2/3 
      Verifying  : jemalloc-3.4.0-1.fc17.x86_64                                                                                                               3/3 
    
    Installed:
      varnish.x86_64 0:3.0.3-5.fc17                                                                                                                               
    
    Dependency Installed:
      jemalloc.x86_64 0:3.4.0-1.fc17                                              varnish-libs.x86_64 0:3.0.3-5.fc17                                             
    
    Complete!
    [root@jmorgan dopey]


Instead of configuring varnish in /etc/default/varnish in systemd/Fedora these configuration values are found in /etc/varnish/varnish.params and are referenced by /usr/lib/systemd/system/varnish.service .

The jist of the setup is varnish starts listening on 80, instead of apache, then it request locally to apache on 8080, if not cached, and then returns the needful back to the client. To accomplish this I needed to change apache to listen on a different port, 8080, and for varnish to listen on 80.

Values changed in /etc/httpd/conf/httpd.conf

    :::bash
    Listen 8080
    NameVirtualHost *:8080
    #Then each virtual host
    <VirtualHost *:8080>

I also needed to change /etc/varnish/varnish.params.

    :::bash
    # Default address and port to bind to. Blank address means all IPv4
    # and IPv6 interfaces, otherwise specify a host name, an IPv4 dotted
    # quad, or an IPv6 address in brackets.
    # VARNISH_LISTEN_ADDRESS=192.168.1.5
    VARNISH_LISTEN_PORT=80

The default port was 6081 which. Not changing this setting but changing apache would result in your site being down hard :)

As Scott and others point out to get your IP's correct in httpd logging you need to add make varnish forward it *and* apache to change it's log format.

I changed this section of /etc/varnish/default.vcl 

    :::bash
     if (req.restarts == 0) {
        if (req.http.x-forwarded-for) {
            set req.http.X-Real-Forwarded-For = req.http.X-Forwarded-For + ", " + regsub(client.ip, ":.*", "");
            unset req.http.X-Forwarded-For;
        } else {
            set req.http.X-Real-Forwarded-For =  regsub(client.ip, ":.*", "");
        }
     }

Initially it tried to set X-Forwarded-For which seemed to fail. I stole Scott's version of the config and it worked like a charm.

For apache, I modified /etc/httpd/conf/httpd.conf to use a new log format. Instead of changing each virtual host, I just changed the default line outside of the virtual host. If I later want to split my site's logs off I will, but for now access.log works. 

    :::bash
    LogFormat "%{X-Real-Forwarded-For}i %l %u %t \"%r\" %>s %b \"%{Referer}i\" \"%{User-Agent}i\"" varnishcombined
    CustomLog logs/access_log varnishcombined


Restart varnish and apache and test your site. We can see varnish is working:

    :::bash
    [root@jmorgan conf] varnishlog 
    ....
    12 SessionOpen  c 213.89.160.129 34289 :80
    12 ReqStart     c 213.89.160.129 34289 376648113
    12 RxRequest    c GET
    12 RxURL        c /
    12 RxProtocol   c HTTP/1.1
    12 RxHeader     c User-Agent: curl/7.37.0
    12 RxHeader     c Host: dopey.io
    12 RxHeader     c Accept: */*
    12 VCL_call     c recv lookup
    12 VCL_call     c hash
    12 Hash         c /
    12 Hash         c dopey.io
    12 VCL_return   c hash
    12 Hit          c 376648063
    12 VCL_call     c hit deliver
    12 VCL_call     c deliver deliver
    12 TxProtocol   c HTTP/1.1
    12 TxStatus     c 200
    12 TxResponse   c OK
    12 TxHeader     c Server: Apache/2.2.23 (Fedora)
    12 TxHeader     c Last-Modified: Sun, 29 Jun 2014 16:45:15 GMT
    12 TxHeader     c ETag: "87ccd-48ac-4fcfc445d7c9c"
    12 TxHeader     c Vary: Accept-Encoding,User-Agent
    12 TxHeader     c Content-Type: text/html; charset=UTF-8
    12 TxHeader     c Transfer-Encoding: chunked
    12 TxHeader     c Date: Sun, 29 Jun 2014 16:52:30 GMT
    12 TxHeader     c X-Varnish: 376648113 376648063
    12 TxHeader     c Age: 84
    12 TxHeader     c Via: 1.1 varnish
    12 TxHeader     c Connection: keep-alive
    12 Gzip         c U D - 4500 18604 80 80 35934
    12 Length       c 18604
    12 ReqEnd       c 376648113 1404060750.428263187 1404060750.428411722 0.000087261 0.000025749 0.000122786
    12 Debug        c herding
    12 SessionClose c no request
    12 StatSess     c 213.89.160.129 34289 0 1 1 0 0 0 353 18604

Notice the "hit deliver", compared to:

    :::bash
    VCL_call     c miss fetch

varnishlog and varnishstat are both quite handy for confirming/triaging your varnish setup. 

Overall a pretty smooth migration to Pelican with a varnish setup. I still have some cleanup of the imported post, such as tags and links. 

