Title: NS2 Dedicated Server - Linux 
Date: 2013-08-29 18:49
Author: Jacob Morgan
Category: Operations
Tags: gaming, configuration
Slug: ns2-dedicated-server-linux

Natural Selection 2 released both client and server version for Linux
this week. Up till now you've had to use wine to host NS2 on Linux, now
you can use Linux. This assumes you are already familiar with steamcmd,
and have it working. Using Fedora below.

You have to use appId ~~[4920][] instead o~~f [4940][]. -- 4940 appId is
now working.

    :::bash  
    #Had an issue with finding 32 bit libspeex libraries. I ended up
    installing the below two rpms which resolved the dep issue. I also had
    to install gtk2.  
    [jpyth@jpyth ns2]$ ls *rpm  
    libspeex1-32bit-1.1.999_1.2rc1-9.1.x86_64.rpm  
    libspeexdsp1-32bit-1.1.999_1.2rc1-35.7.x86_64.rpm  
    [jpyth@jpyth ns2]$
    
    [jpyth@jpyth steamcmd]$ ./steamcmd.sh +login LOGIN PASSWORD
    +force_install_dir PATH +app_update 4940 validate  
    #example -- Installs Natural Selection 2, into a directory 'ns2' one
    directory up from my current path.  
    [jpyth@jpyth steamcmd]$ ./steamcmd.sh +login myrealemail@hotmail.com
    noway01 +force_install_dir ../ns2/ +app_update 4940 validate  
    #Start the game:  
    [jpyth@jpyth steamcmd]$ cd ../ns2  
    #start server  
    [jpyth@jpyth ns2]$ ./server_linux32 -ip 198.27.65.132 -port 27035
    -map ns2_tram -name "jpyth.com|NS2|#1" -limit 24 -console
    -config_path ./config
    
    #I launch mine with screen, using this script:  
    [jpyth@jpyth bin]$ cat ns2  
    #!/bin/sh  
    name="$1"  
    port=$2  
    cd /home/jpyth/ns2  
    screen -d -m -S ns2 ./server_linux32 -ip 198.27.65.132 -port $port
    -map ns2_tram -name "$name" -limit 24 -console -config_path ./config  
    #example  
    [jpyth@jpyth bin]$ ns2 "jpyth.com|NS2|#1" 27030  
    [jpyth@jpyth bin]$
    

Currently hosting two NS2 servers. Can find them below:  
[One][]  
[Two][]

  [4920]: http://steamdb.info/app/4920/ "4920"
  [4940]: http://steamdb.info/app/4940/ "4940"
  [One]: http://www.gametracker.com/server_info/198.27.65.132:27015/
    "One"
  [Two]: http://www.gametracker.com/server_info/198.27.65.132:27020/
    "Two"
