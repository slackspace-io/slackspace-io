Title: Logitech g13 on Linux (Arch)
Date: 2013-04-01 10:35
Author: Jacob Morgan
Category: Operations
Tags: AUR, gaming, configuration
Slug: logitech-g13-on-linux-arch

I've gamed on Linux for a few years, and about a year ago I picked up a
[Logitech G13][]. I've used the [g13-git][] package from AUR without
issue.

I remember information about g13 on Linux being a bit sparse/out-of-date
when I first did it on my desktop, now that I'm doing it on my laptop I
thought i'd share the steps/issues I encounter.

If you aren't running Arch, here is a link to [the github g13 on Linux
github project.][] The rest of this doc will assume you are running Arch
Linux, but should help any Linux user.

Obtaining [g13-git from AUR][g13-git], building, and installing:  

    :::bash
    #get the .tar.gz from AUR && extract && cd $tar  
    [jpyth@arch-jpyth aur]$ wget
    https://aur.archlinux.org/packages/g1/g13-git/g13-git.tar.gz  
    --2013-04-01 05:06:04--
    https://aur.archlinux.org/packages/g1/g13-git/g13-git.tar.gz  
    Resolving aur.archlinux.org (aur.archlinux.org)... 78.46.78.247,
    2a01:4f8:120:34c2::2  
    Connecting to aur.archlinux.org
    (aur.archlinux.org)|78.46.78.247|:443... connected.  
    HTTP request sent, awaiting response... 200 OK  
    Length: 694 [application/x-gzip]  
    Saving to: ‘g13-git.tar.gz’
    
    100%[=========================================================================================================================================================================>]
    694 --.-K/s in 0s
    
    2013-04-01 05:06:05 (99.8 MB/s) - ‘g13-git.tar.gz’ saved [694/694]
    
    [jpyth@arch-jpyth aur]$ tar -xvf g13-git.tar.gz  
    g13-git/  
    g13-git/PKGBUILD  
    [jpyth@arch-jpyth aur]$ cd g13-git  
    #makepkg  
    [jpyth@arch-jpyth g13-git]$ makepkg  
    ==> Determining latest git revision...  
    -> Version found: 20130401  
    ==> Making package: g13-git 20130401-1 (Mon Apr 1 05:06:13 PDT 2013)  
    ==> Checking runtime dependencies...  
    ==> Checking buildtime dependencies...  
    ==> Retrieving Sources...  
    ==> Extracting Sources...  
    ==> Starting build()...  
    ==> Connecting to GIT server....  
    Cloning into 'g13'...  
    remote: Counting objects: 61, done.  
    remote: Compressing objects: 100% (58/58), done.  
    remote: Total 61 (delta 26), reused 28 (delta 2)  
    Unpacking objects: 100% (61/61), done.  
    ==> GIT checkout done or server timeout  
    ==> Starting make...  
    Cloning into '/home/jpyth/aur/g13-git/src/g13-build'...  
    done.  
    g++ -o g13 -std=c++0x g13.cc -lusb-1.0  
    g13.cc:4:34: fatal error: boost/lexical_cast.hpp: No such file or
    directory  
    compilation terminated.  
    #This looks bad  
    make: *** [g13] Error 1  
    ==> ERROR: A failure occurred in build().  
    Aborting...  
    [jpyth@arch-jpyth g13-git]$  
    #Install boost to fix error -- The AUR package needs to be updated for
    the dependency.  
    [jpyth@arch-jpyth ~]$ pacman -S boost boost-libs  
    #Much better /cheer  
    [jpyth@arch-jpyth g13-git]$ makepkg  
    ==> Determining latest git revision...  
    -> Version found: 20130401  
    ==> Making package: g13-git 20130401-1 (Mon Apr 1 05:11:45 PDT 2013)  
    ==> Checking runtime dependencies...  
    ==> Checking buildtime dependencies...  
    ==> Retrieving Sources...  
    ==> Extracting Sources...  
    ==> Starting build()...  
    ==> Connecting to GIT server....  
    Cloning into 'g13'...  
    remote: Counting objects: 61, done.  
    remote: Compressing objects: 100% (58/58), done.  
    remote: Total 61 (delta 26), reused 28 (delta 2)  
    Unpacking objects: 100% (61/61), done.  
    ==> GIT checkout done or server timeout  
    ==> Starting make...  
    Cloning into '/home/jpyth/aur/g13-git/src/g13-build'...  
    done.  
    g++ -o g13 -std=c++0x g13.cc -lusb-1.0  
    g++ -o pbm2lpbm pbm2lpbm.c  
    ==> Entering fakeroot environment...  
    ==> Starting package()...  
    ==> Tidying install...  
    -> Purging unwanted files...  
    -> Compressing man and info pages...  
    -> Stripping unneeded symbols from binaries and libraries...  
    ==> Creating package...  
    -> Generating .PKGINFO file...  
    -> Compressing package...  
    ==> Leaving fakeroot environment.  
    ==> Finished making: g13-git 20130401-1 (Mon Apr 1 05:11:51 PDT 2013)  
    #Install your shiny new package:D  
    [jpyth@arch-jpyth g13-git]$ sudo pacman -U
    g13-git-20130401-1-x86_64.pkg.tar.xz  

The configuration is detailed on the github project page [here][].In
short to get working, you need a file with mappings from G13 to 'known'
keys that you dump into a pipe(/tmp/g13-0) one way or another, the
daemon will read from when started. When you start the daemon the
available g13 keys and available system keys to map to are printed to
stdout. An easy way to do this is a file with the binds that you simply
*cat* into the pipe.  

    :::bash
    #start g13 daemon without a g13 connected - notice the known G13 keys,
    and available keys to map.  
    [jpyth@arch-jpyth jmorgan]# /usr/bin/g13  
    Known keys on G13:  
    BD DOWN G1 G10 G11 G12 G13 G14 G15 G16 G17 G18 G19 G2 G20 G21 G22 G3 G4
    G5 G6 G7 G8 G9 L1 L2 L3 L4 LEFT LIGHT LIGHT_STATE M1 M2 M3 MR TOP
    STICK_LEFT STICK_RIGHT STICK_UP STICK_DOWN  
    Known keys to map to:  
    KEY_0 KEY_1 KEY_2 KEY_3 KEY_4 KEY_5 KEY_6 KEY_7 KEY_8 KEY_9
    KEY_A KEY_APOSTROPHE KEY_B KEY_BACKSLASH KEY_BACKSPACE KEY_C
    KEY_CAPSLOCK KEY_COMMA KEY_D KEY_DELETE KEY_DOT KEY_DOWN KEY_E
    KEY_END KEY_ENTER KEY_EQUAL KEY_ESC KEY_F KEY_F1 KEY_F10 KEY_F2
    KEY_F3 KEY_F4 KEY_F5 KEY_F6 KEY_F7 KEY_F8 KEY_F9 KEY_G
    KEY_GRAVE KEY_H KEY_HOME KEY_I KEY_INSERT KEY_J KEY_K KEY_KP0
    KEY_KP1 KEY_KP2 KEY_KP3 KEY_KP4 KEY_KP5 KEY_KP6 KEY_KP7 KEY_KP8
    KEY_KP9 KEY_KPASTERISK KEY_KPDOT KEY_KPMINUS KEY_KPPLUS KEY_L
    KEY_LEFT KEY_LEFTALT KEY_LEFTBRACE KEY_LEFTCTRL KEY_LEFTSHIFT
    KEY_M KEY_MINUS KEY_N KEY_NUMLOCK KEY_O KEY_P KEY_PAGEDOWN
    KEY_PAGEUP KEY_Q KEY_R KEY_RIGHT KEY_RIGHTALT KEY_RIGHTBRACE
    KEY_RIGHTCTRL KEY_RIGHTSHIFT KEY_S KEY_SCROLLLOCK KEY_SEMICOLON
    KEY_SLASH KEY_SPACE KEY_T KEY_TAB KEY_U KEY_UP KEY_V KEY_W
    KEY_X KEY_Y KEY_Z  
    Found 0 G13s  
    
I use one mapping for all games, but you could have different binds per
game. Here is mine:  
:::bash  
#bind $g13Key $mappedKey  
[jpyth@arch-jpyth ~]$ cat wowBinds  
bind G1 KEY_7  
bind G2 KEY_8  
bind G3 KEY_9  
bind G4 KEY_0  
bind G5 KEY_KPMINUS  
bind G6 KEY_Q  
bind G7 KEY_E  
bind G8 KEY_TAB  
bind G9 KEY_1  
bind G10 KEY_2  
bind G11 KEY_3  
bind G12 KEY_4  
bind G13 KEY_5  
bind G14 KEY_6  
bind G15 KEY_LEFTSHIFT  
bind G16 KEY_R  
bind G17 KEY_T  
bind G18 KEY_Y  
bind G19 KEY_F  
bind G20 KEY_LEFTALT  
bind G21 KEY_C  
bind G22 KEY_V  
bind L4 KEY_B  
bind L3 KEY_M  
bind MR KEY_F6  
bind M3 KEY_F5  
bind M2 KEY_F4  
bind M1 KEY_F3  
bind LEFT KEY_RIGHTBRACE  
bind DOWN KEY_SPACE  
bind STICK_LEFT KEY_A  
bind STICK_RIGHT KEY_D  
bind STICK_UP KEY_W  
bind STICK_DOWN KEY_S  
[jpyth@arch-jpyth ~]$  

Confirming it works with a g13 attached  

    :::bash  
    #cat bind file into g13-0 pipe, which will be loaded when the g13
    daemon start.  
    [jpyth@arch-jpyth jmorgan]# cat wowBinds > /tmp/g13-0  
    #start with g13 connected  
    [jpyth@arch-jpythg jpyth]# /usr/bin/g13  
    Known keys on G13:  
    BD DOWN G1 G10 G11 G12 G13 G14 G15 G16 G17 G18 G19 G2 G20 G21 G22 G3 G4
    G5 G6 G7 G8 G9 L1 L2 L3 L4 LEFT LIGHT LIGHT_STATE M1 M2 M3 MR TOP
    STICK_LEFT STICK_RIGHT STICK_UP STICK_DOWN  
    Known keys to map to:  
    KEY_0 KEY_1 KEY_2 KEY_3 KEY_4 KEY_5 KEY_6 KEY_7 KEY_8 KEY_9
    KEY_A KEY_APOSTROPHE KEY_B KEY_BACKSLASH KEY_BACKSPACE KEY_C
    KEY_CAPSLOCK KEY_COMMA KEY_D KEY_DELETE KEY_DOT KEY_DOWN KEY_E
    KEY_END KEY_ENTER KEY_EQUAL KEY_ESC KEY_F KEY_F1 KEY_F10 KEY_F2
    KEY_F3 KEY_F4 KEY_F5 KEY_F6 KEY_F7 KEY_F8 KEY_F9 KEY_G
    KEY_GRAVE KEY_H KEY_HOME KEY_I KEY_INSERT KEY_J KEY_K KEY_KP0
    KEY_KP1 KEY_KP2 KEY_KP3 KEY_KP4 KEY_KP5 KEY_KP6 KEY_KP7 KEY_KP8
    KEY_KP9 KEY_KPASTERISK KEY_KPDOT KEY_KPMINUS KEY_KPPLUS KEY_L
    KEY_LEFT KEY_LEFTALT KEY_LEFTBRACE KEY_LEFTCTRL KEY_LEFTSHIFT
    KEY_M KEY_MINUS KEY_N KEY_NUMLOCK KEY_O KEY_P KEY_PAGEDOWN
    KEY_PAGEUP KEY_Q KEY_R KEY_RIGHT KEY_RIGHTALT KEY_RIGHTBRACE
    KEY_RIGHTCTRL KEY_RIGHTSHIFT KEY_S KEY_SCROLLLOCK KEY_SEMICOLON
    KEY_SLASH KEY_SPACE KEY_T KEY_TAB KEY_U KEY_UP KEY_V KEY_W
    KEY_X KEY_Y KEY_Z  
    Kernel driver detached  
    Found 1 G13s  
    command: bind G1 KEY_7  
    command: bind G2 KEY_8  
    command: bind G3 KEY_9  
    command: bind G4 KEY_0  
    command: bind G5 KEY_KPMINUS  
    command: bind G6 KEY_Q  
    command: bind G7 KEY_E  
    command: bind G8 KEY_TAB  
    command: bind G9 KEY_1  
    command: bind G10 KEY_2  
    command: bind G11 KEY_3  
    command: bind G12 KEY_4  
    command: bind G13 KEY_5  
    command: bind G14 KEY_6  
    command: bind G15 KEY_LEFTSHIFT  
    command: bind G16 KEY_R  
    command: bind G17 KEY_T  
    command: bind G18 KEY_Y  
    command: bind G19 KEY_F  
    command: bind G20 KEY_LEFTALT  
    command: bind G21 KEY_C  
    command: bind G22 KEY_V  
    command: bind L4 KEY_B  
    command: bind L3 KEY_M  
    command: bind MR KEY_F6  
    command: bind M3 KEY_F5  
    command: bind M2 KEY_F4  
    command: bind M1 KEY_F3  
    command: bind LEFT KEY_RIGHTBRACE  
    command: bind DOWN KEY_SPACE  
    command: bind STICK_LEFT KEY_A  
    command: bind STICK_RIGHT KEY_D  
    command: bind STICK_UP KEY_W  
    command: bind STICK_DOWN KEY_S  

At this point, you should have a working gamepad. If you do not map any
keys by default all keys are mapped to 'a', I noticed the daemon would
crash if left this way after a period of time. My assumption at the
time(year+ ago) was due to having too many(or just >1) key mapped to
the same. I never investigated/tested though, instead I started dumping
my binds into the pipe before starting the daemon and the issue went
away entirely. I haven't had an issue since(over a year). The only time
the gamepad does not function is if I forget to start the daemon after a
power cycle, or disconnect/reconnect the device.

Here is the current script I run after each time I restart my computer:  

    :::bash  
    [jpyth@arch-jm ~]$ cat startG13.sh  
    #!/bin/bash
    
    ps aux | grep g13 | grep -v grep &>/dev/null && echo "Already running-
    Exiting"|| {  
    cat /home/jpyth/g13/wowBinds > /tmp/g13-0  
    nohup /usr/bin/g13 &  
    rm nohup.out  
    }  

Pretty crude, I know. I started to add it as a systemd process, but
there is currently a bug in the daemon. If you disconnect the g13 the
daemon goes into a tight loop failing to find the device. This will
consume process, and will have to be restarted once the device is
reconnected anyway. I didn't want to write a wrapper to babysit the
daemon....I have another plan for it by adding a bit more to onConnect,
when I get that working i'll share;).

  [Logitech G13]: http://www.newegg.com/Product/Product.aspx?Item=N82E16823126050
  [g13-git]: https://aur.archlinux.org/packages/g13-git/
  [the github g13 on Linux github project.]: https://github.com/ecraven/g13
  [here]: https://github.com/ecraven/g13/blob/master/README
