Title: Starting Wireless On Boot With systemd
Date: 2013-03-27 07:40
Author: Jacob Morgan
Category: Operations
Tags: network, systemd, automation
Slug: add-wpa_supplicant-to-systemd

I didn't want to use a GUI to manage my wireless network. Majority of
the time I will be at known locations, and when needing to join a new
network doing it via command line is not difficult.

I have known networks configured, with dhcpcd and wpa_supplicant
started during bootup by systemd. I also chose to log, and rotate,
wpa_supplicant to help debug any network issues I might encounter. The
following was done in Arch.

A few files must exist, the first is a .service file under /usr/lib/systemd/system/. The Type=forking is required since we are starting a daemon, the PIDFile is optional. If you include it, make sure you actually create a pid file when starting the process.  

    :::bash
    #cat /usr/lib/systemd/system/wireless.service  
    [Unit]  
    Description=Wireless
    
    [Service]  
    Type=forking  
    PIDFILE=/var/run/wireless.pid  
    ExecStart=/usr/lib/systemd/scripts/wirelessStart  
    ExecReload=/usr/lib/systemd/scripts/wirelessRestart  
    ExecStop=/usr/lib/systemd/scripts/wirelessStop
    
    [Install]  
    WantedBy=multi-user.target  
    
I chose to make individual Start, Restart and Stop scripts as seen above. You may do this differently. Here are mine:  

    :::bash  
    #Start Script  
    #cat /usr/lib/systemd/scripts/wirelessStart  
    #!/bin/bash  
    ps aux | grep wpa_supplicant | grep -v grep &>/dev/null || {  
    wpa_supplicant -f /var/log/wpa_supplicant/wpa_supplicant.log -B -i
    wlp8s0 -P/var/run/wireless.pid -c /etc/wpa_supplicant.conf -d  
    }
    
    #Stop Script - I haven't had an issue with pkill failing to get the job done.  
    #cat /usr/lib/systemd/scripts/wirelessStop  
    #!/bin/bash  
    pkill wpa_supplicant
    
    #Restart Script  
    #cat /usr/lib/systemd/scripts/wirelessRestart  
    #!/bin/bash  
    /usr/lib/systemd/scripts/wirelessStop  
    sleep 3  
    /usr/lib/systemd/scripts/wirelessStart

logrotate:  

    :::bash
    #cat /etc/logrotate.d/wpa_supplicant  
    /var/log/wpa_supplicant/*log {  
    copytruncate  
    compress  
    notifempty  
    missingok  
    }  
    
Enabling the new service, and checking status:  

    :::bash  
    #enable the service  
    [jpyth@arch-jpyth ~]$ sudo systemctl enable wireless  
    ln -s '/usr/lib/systemd/system/wireless.service'
    '/etc/systemd/system/multi-user.target.wants/wireless.service'  
    #start the service  
    [jpyth@arch-jpyth ~]$ sudo systemctl start wireless  
    #status of service  
    [jpyth@arch-jpyth ~]$ sudo systemctl status wireless  
    wireless.service - Wireless  
    Loaded: loaded (/usr/lib/systemd/system/wireless.service; enabled)  
    Active: active (running) since Wed 2013-03-27 07:29:19 PDT; 3s ago  
    Process: 1574 ExecStart=/usr/lib/systemd/scripts/wirelessStart
    (code=exited, status=0/SUCCESS)  
    Main PID: 1579 (wpa_supplicant)  
    CGroup: name=systemd:/system/wireless.service  
    └─1579 wpa_supplicant -f /var/log/wpa_supplicant/wpa_supplicant.log
    -B -i wlp8s0 -P/var/run/wireless.pid -c /etc/wpa_supplicant.conf -d
    
    Mar 27 07:29:18 arch-jpyth systemd[1]: Starting Wireless...  
    Mar 27 07:29:19 arch-jpyth systemd[1]: Started Wireless.  
    [jpyth@arch-jpyth ~]$ psg wpa_supplican  
    root 1579 0.0 0.0 32328 1344 ? Ss 07:29 0:00 wpa_supplicant -f
    /var/log/wpa_supplicant/wpa_supplicant.log -B -i wlp8s0
    -P/var/run/wireless.pid -c /etc/wpa_supplicant.conf -d  
    #log file  
    [jpyth@arch-jpyth ~]$ tail
    /var/log/wpa_supplicant/wpa_supplicant.log  
    EAP: EAP entering state DISABLED  
    EAPOL: SUPP_PAE entering state AUTHENTICATED  
    EAPOL: Supplicant port status: Authorized  
    EAPOL: SUPP_BE entering state IDLE  
    EAPOL authentication completed successfully  
    RTM_NEWLINK: operstate=1 ifi_flags=0x11043
    ([UP][RUNNING][LOWER_UP])  
    RTM_NEWLINK, IFLA_IFNAME: Interface 'wlp8s0' added  
    WEXT: if_removed already cleared - ignore event  
    EAPOL: startWhen --> 0  
    EAPOL: disable timer tick  
    [jpyth@arch-jpyth ~]$  
    
*note if you need dhcpcd enable/verify:  

    :::bash  
    #enable the service  
    [jpyth@arch-jpyth ~]$ sudo systemctl enable dhcpcd  
    ln -s '/usr/lib/systemd/system/dhcpcd.service'
    '/etc/systemd/system/multi-user.target.wants/dhcpcd.service'  
    #start the service  
    [jpyth@arch-jpyth ~]$ sudo systemctl start dhcpcd  
    #status of the service  
    [jpyth@arch-jpyth ~]$ sudo systemctl status dhcpcd  
    dhcpcd.service - dhcpcd on all interfaces  
    Loaded: loaded (/usr/lib/systemd/system/dhcpcd.service; enabled)  
    Active: active (running) since Wed 2013-03-27 07:32:06 PDT; 4s ago  
    Process: 1697 ExecStart=/sbin/dhcpcd -q -b (code=exited,
    status=0/SUCCESS)  
    Main PID: 1698 (dhcpcd)  
    CGroup: name=systemd:/system/dhcpcd.service  
    └─1698 /sbin/dhcpcd -q -b
    
    Mar 27 07:32:06 arch-jpyth systemd[1]: Started dhcpcd on all
    interfaces.  
    Mar 27 07:32:06 arch-jpyth dhcpcd[1698]: wlp8s0: sending IPv6 Router
    Solicitation  
    Mar 27 07:32:06 arch-jpyth dhcpcd[1698]: wlp8s0: rebinding lease of
    192.168.1.42  
    Mar 27 07:32:06 arch-jpyth dhcpcd[1698]: enp7s0: waiting for carrier  
    Mar 27 07:32:06 arch-jpyth dhcpcd[1698]: wlp8s0: acknowledged
    192.168.1.42 from 192.168.1.1  
    Mar 27 07:32:06 arch-jpyth dhcpcd[1698]: wlp8s0: checking for
    192.168.1.42  
    Mar 27 07:32:10 arch-jpyth dhcpcd[1698]: wlp8s0: sending IPv6 Router
    Solicitation  
    [jpyth@arch-jpyth ~]$  

