Title: Dogecoin Ubuntu Live CD Installer
Date: 2014-01-27 03:30
Author: Jacob Morgan
Category: Operations
Tags: automation, dogecoin, ubuntu
Slug: dogecoin-ubuntu-live-cd-installer

I spent a little time today trying to come up with a single tar file and
script to install dogecoin-qt on an Ubuntu live CD. This will let fellow
shibes make offline wallets for better security!

You can get the code and instructions from my github [here][].

This version is created for 64-bit Ubuntu 12.04 Live CD. I likely have
too many deps in the tar file as it is quite huge(~80MB), but it works!
I'll work on shaving it down soon.

Here is what it looks like in action:

    :::bash  
    [shibe@ubuntu mnt]$ sudo tar -xvf
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install.tar  
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqt4-xml_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libx11-6_2%3a1.4.99.1-0ubuntu2.2_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libssl1.0.0_1.0.1-4ubuntu5.11_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libboost-system1.48.0_1.48.0-3_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libdrm-dev_2.4.46-1ubuntu0.0.0.1_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libboost-system1.48-dev_1.48.0-3_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqtwebkit-dev_2.2.1-1ubuntu4_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqt4-scripttools_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/x11proto-input-dev_2.3-1~precise1_all.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/gcc-4.4_4.4.7-1ubuntu2_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/qt4-linguist-tools_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libstdc++6-4.6-dev_4.6.3-1ubuntu5_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libdrm-intel1_2.4.46-1ubuntu0.0.0.1_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libalgorithm-merge-perl_0.08-2_all.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libdrm2_2.4.46-1ubuntu0.0.0.1_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libdb++-dev_5.1.4ubuntu1_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libdb5.1++-dev_5.1.25-11build1_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libglu1-mesa_8.0.4-0ubuntu0.7_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/x11proto-kb-dev_1.0.5-2_all.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libxcb1-dev_1.8.1-1ubuntu0.2_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/g++-4.6_4.6.3-1ubuntu5_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqt4-qt3support_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqtcore4_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libboost-filesystem1.48-dev_1.48.0-3_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/x11proto-xext-dev_7.2.0-3_all.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libboost-date-time1.48-dev_1.48.0-3_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/g++-4.4_4.4.7-1ubuntu2_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqt4-sql_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/qt4-qmake_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/zlib1g-dev_1%3a1.2.3.4.dfsg-3ubuntu4_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libtimedate-perl_1.2000-1_all.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libkms1_2.4.46-1ubuntu0.0.0.1_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqt4-script_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/cpp-4.4_4.4.7-1ubuntu2_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libx11-doc_2%3a1.4.99.1-0ubuntu2.2_all.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libgl1-mesa-dev_8.0.4-0ubuntu0.7_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/x11proto-core-dev_7.0.22-1ubuntu0.1_all.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqt4-svg_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libxext-dev_2%3a1.3.0-3ubuntu0.1_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqt4-designer_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libpthread-stubs0_0.3-3_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libxau-dev_1%3a1.0.6-4_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libboost-serialization1.48.0_1.48.0-3_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqtgui4_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/mesa-common-dev_8.0.4-0ubuntu0.7_amd64.deb  
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/xtrans-dev_1.2.6-2_all.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libdrm-radeon1_2.4.46-1ubuntu0.0.0.1_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libboost-date-time1.48.0_1.48.0-3_amd64.deb  
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/dogecoin-master-1.5.zip  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libx11-dev_2%3a1.4.99.1-0ubuntu2.2_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqt4-dev_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqt4-opengl-dev_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libdrm-nouveau2_2.4.46-1ubuntu0.0.0.1_amd64.deb  
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/j  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libpthread-stubs0-dev_0.3-3_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libglu1-mesa-dev_8.0.4-0ubuntu0.7_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libdrm-nouveau1a_2.4.46-1ubuntu0.0.0.1_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libstdc++6-4.4-dev_4.4.7-1ubuntu2_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/dpkg-dev_1.16.1.2ubuntu7.2_all.deb  
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/install.sh  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libdb5.1++_5.1.25-11build1_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqtwebkit4_2.2.1-1ubuntu4_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqt4-network_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libalgorithm-diff-xs-perl_0.04-2build2_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqt4-help_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libboost-thread1.48-dev_1.48.0-3_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/build-essential_11.5ubuntu2.1_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqt4-test_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqt4-sql-sqlite_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libssl-doc_1.0.1-4ubuntu5.11_all.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/qdbus_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libboost-chrono1.48.0_1.48.0-3_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libxdmcp-dev_1%3a1.1.0-4_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/g++_4%3a4.6.3-1ubuntu5_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libboost-serialization1.48-dev_1.48.0-3_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libboost-filesystem1.48.0_1.48.0-3_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libboost1.48-dev_1.48.0-3_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libboost-chrono1.48-dev_1.48.0-3_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libboost-program-options1.48.0_1.48.0-3_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libdpkg-perl_1.16.1.2ubuntu7.2_all.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libminiupnpc-dev_1.6-3ubuntu1_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqt4-xmlpatterns_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libssl-dev_1.0.1-4ubuntu5.11_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqt4-dbus_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libboost-thread1.48.0_1.48.0-3_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqt4-declarative_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libalgorithm-diff-perl_1.19.02-2_all.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/gcc-4.4-base_4.4.7-1ubuntu2_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/xorg-sgml-doctools_1%3a1.10-1_all.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libdb5.1-dev_5.1.25-11build1_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libboost-program-options1.48-dev_1.48.0-3_amd64.deb  
    
    dogecoin-qt-1.5-ubuntu-12.04-Live-Install/libqt4-opengl_4%3a4.8.1-0ubuntu4.6_amd64.deb  
    [shibe@ubuntu mnt]$ cd dogecoin-qt-1.5-ubuntu-12.04-Live-Install  
    [shibe@ubuntu dogecoin-qt-1.5-ubuntu-12.04-Live-Install]$
    ./install.sh  
    First step to the moon in(much time needed)  
    3  
    2  
    1  
    ##Lots of stuff.. This will take a while.  

Here is the install.sh script to install the deps, extract and compile
the source code for dogecoin-qt.  

    :::bash  
    [shibe@ubuntu dogecoin-qt-1.5-ubuntu-12.04-Live-Install]$ cat
    install.sh
    
    #!/bin/sh
    
    #Check jpyth.com or github.com/jpyth for any updates should dogecoin-qt
    have a newer version! To the moon!
    
    echo "First step to the moon in(much time needed)"  
    echo "3"  
    sleep 1  
    echo "2"  
    sleep 1  
    echo "1"  
    sleep 1  
    dpkg -i *deb && {  
    echo "Files need"  
    unzip dogecoin-master-1.5.zip && {  
    cd dogecoin-master-1.5 && {  
    echo "funny make"  
    ls  
    qmake && {  
    echo "make time"  
    ls  
    make && {  
    echo "Much Finish, 'cd dogecoin-master-1.5; ./dogecoin-qt' to run
    dogecoin! To the moon!"  
    } || {  
    echo "Fail much make, much sad. Report logs above."  
    }  
    } || {  
    echo "Fail much qmake, much sad. Report. "  
    }  
    } || {  
    echo "Moving too hard"  
    }  
    } || {  
    echo "Much weird, dogecoin-master-1.5.zip much sad"  
    }  
    } || {  
    echo "dpkg no like, find a shibe to help with errors above!"  
    }  
    [shibe@ubuntu dogecoin-qt-1.5-ubuntu-12.04-Live-Install]$  

Nothing special, just going through each step and checking return code
on the prior step. Hopefully most of those error messages are never seen
by anyone.

If you find any deps that are missing, or know of ones I can remove
please let me know. I'll write up information about creating the live
CD, other steps, and dogecoin in general...but wanted to share this as I
promised it on reddit.

To the moon!

Doge: D8kWKgF2x6gsiZzRyXgonogPQuRMxf6qtp

  [here]: https://github.com/jpyth/doge "DogeCoin Live CD"
