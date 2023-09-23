Title: onConnect Monitor/Adjust System Configuration Based on Network
Date: 2013-03-29 10:43
Author: Jacob Morgan
Category: Code
Tags: automation, python
Slug: onconnect-monitoradjust-system-configuration-based-on-network

I initially just wanted to adjust my sound depending on whether I was at
work, or at home on my laptop. I decided to try and write something I
could plug additional things into over time, to automate any task I
normally do when moving between known networks.

This is also the first time I've shared something I wrote on github. I'm
sure I broke every rule in the book on best practices, as I am self
taught/not a developer. Feel free to check it out though, [OnConnect
GitHub][]. I welcome any feedback.:)

I'm not sure I like using the MAC for this, I debated using ESSID and
may very well go back to that. I don't like the case of finding more
than one MAC that might have a config found(work), but I also wanted to
read/parse from a file though not a command+stdout parsing. I couldn't
find a reliable method for obtaining the ESSID from a file, yet.

Some example of it working, for me atleast:)  
    :::bash  
    #no network, no macs found, default config  
    [jpyth@arch-jpyth ~]$ ip addr | grep -A1 wlp8s0  
    3: wlp8s0: \<BROADCAST,MULTICAST> mtu 1500 qdisc mq state DOWN qlen
    1000  
    link/ether c4:85:08:fc:91:85 brd ff:ff:ff:ff:ff:ff  
    [jpyth@arch-jpyth ~]$  
    [jpyth@arch-jpyth ~]$ sudo systemctl status onConnect  
    onConnect.service - onConnect  
    Loaded: loaded (/usr/lib/systemd/system/onConnect.service; enabled)  
    Active: active (running) since Fri 2013-03-29 10:29:42 PDT; 2s ago  
    Process: 7886 ExecStop=/usr/lib/systemd/scripts/onConnectStop
    (code=killed, signal=TERM)  
    Process: 7898 ExecStart=/usr/lib/systemd/scripts/onConnectStart
    (code=exited, status=0/SUCCESS)  
    Main PID: 7905 (onConnect)  
    CGroup: name=systemd:/system/onConnect.service  
    └─7905 /usr/bin/python2 /usr/bin/onConnect
    
    Mar 29 10:29:42 arch-jpyth systemd[1]: Starting onConnect...  
    Mar 29 10:29:42 arch-jpyth systemd[1]: Started onConnect.  
    [jpyth@arch-jpyth ~]$ tail /var/log/onConnect/onConnect.log  
    INFO:root:Config found for Default  
    DEBUG:root:\<function doVolume at 0x7fd4c1dd30c8>  
    INFO:root:command amixer cset iface=MIXER,name='Master Playback Volume'
    60% for Default  
    DEBUG:root:\<function doVpn at 0x7fd4c1dd3140>  
    INFO:root:command systemctl stop vpn for Default  
    [jpyth@arch-jpyth ~]$  
    #vpn being stopped:  
    [jpyth@arch-jpyth ~]$ sudo systemctl status vpn  
    vpn.service - OpenVpn For Work  
    Loaded: loaded (/usr/lib/systemd/system/vpn.service; disabled)  
    Active: inactive (dead)
    
    Mar 29 10:29:42 arch-jpyth systemd[1]: Stopping OpenVpn For Work...  
    Mar 29 10:29:43 arch-jpyth systemd[1]: Stopped OpenVpn For Work.  
    [jpyth@arch-jpyth ~]$
    
    #Now starting/joining wireless network  
    [jpyth@arch-jpyth ~]$ sudo systemctl start wireless  
    [jpyth@arch-jpyth ~]$ systemctl status wireless  
    wireless.service - Wireless  
    Loaded: loaded (/usr/lib/systemd/system/wireless.service; enabled)  
    Active: active (running) since Fri 2013-03-29 10:34:37 PDT; 5s ago  
    Process: 7692 ExecStop=/usr/lib/systemd/scripts/wirelessStop
    (code=exited, status=0/SUCCESS)  
    Process: 7942 ExecStart=/usr/lib/systemd/scripts/wirelessStart
    (code=exited, status=0/SUCCESS)  
    Main PID: 7948 (wpa_supplicant)  
    CGroup: name=systemd:/system/wireless.service  
    └─7948 /usr/sbin/wpa_supplicant -f
    /var/log/wpa_supplicant/wpa_supplicant.log -B -i wlp8s0
    -P/var/run/wireless.pid -c /etc/wpa_supplicant.conf -d  
    [jpyth@arch-jpyth ~]$
    
    #onConnect log file  
    INFO:root:['FF:FF:FF:FF:FF:FF', 'Default']  
    INFO:root:Config found for FF:FF:FF:FF:FF:FF  
    ['description', 'location', 'volume', 'vpn']  
    DEBUG:root:\<function doVolume at 0x7fd4c1dd30c8>  
    INFO:root:command amixer cset iface=MIXER,name='Master Playback Volume'
    85% for FF:FF:FF:FF:FF:FF  
    DEBUG:root:\<function doVpn at 0x7fd4c1dd3140>  
    INFO:root:command systemctl start vpn for FF:FF:FF:FF:FF:FF
    
    #vpn started automatically when joined:  
    [jpyth@arch-jpyth ~]$ sudo systemctl status vpn  
    vpn.service - OpenVpn For Work  
    Loaded: loaded (/usr/lib/systemd/system/vpn.service; disabled)  
    Active: active (running) since Fri 2013-03-29 10:34:57 PDT; 1min 22s
    ago  
    Process: 7978 ExecStart=/usr/lib/systemd/scripts/vpnStart (code=exited,
    status=0/SUCCESS)  
    Main PID: 7986 (screen)  
    CGroup: name=systemd:/system/vpn.service
    
    Mar 29 10:34:57 arch-jpyth systemd[1]: Starting OpenVpn For Work...  
    Mar 29 10:34:57 arch-jpyth systemd[1]: Started OpenVpn For Work.  
    [jpyth@arch-jpyth ~]$  

I think it'll do what I need=D

  [OnConnect GitHub]: https://github.com/jpyth/onConnect
    "OnConnect GitHUb"
