Title: Wireshark Fun
Date: 2013-07-26 22:46
Author: Jacob Morgan
Category: Operations
Tags: triage
Slug: wireshark-fun

Quick and dirty way to configure a wireless router(Airport Express), to
use your linux pc as a network gateway. I needed to sniff the network of
some wireless devices, which can be quite handy.

You'll need to do a few things:  
1.) Enable IP Forwarding  
2.) Configure iptables to nat/forward traffic from in one interface and
out another  
3.) Bridge the device, and use dhcpd to handle dhcp for the
devices(Much easier to track)

Enable IP Forwarding:  

    :::bash  
    echo 1 > /proc/sys/net/ipv4/ip_forward  

Configure iptables(as root):  

    :::bash  
    #Interface you are sending traffic out(eth0)  
    #Going out usb-ethernet  
    iptables --table nat --append POSTROUTING --out-interface eth0 -j
    MASQUERADE  
    #Going out my wireless  
    iptables --table nat --append POSTROUTING --out-interface wlp8s0 -j
    MASQUERADE
    
    #Interface you are forwarding traffic from(Airport hooked to this
    interface)  
    iptables --append FORWARD --in-interface enp7s0 -j ACCEPT  

Configure dhcpd4 to hand out dhcpcd on enp7s0  

    :::bash  
    #Replace xxx IP with valid dns servers.  
    [jmorgan@arch-dopey ~]$ cat /etc/dhcpd.conf  
    option domain-name "arch-dopey.com";  
    option domain-name-servers xxx.xxx.xxx, xxx.xxx.xxx;  
    default-lease-time 14440;  
    ddns-update-style none;  
    deny bootp; shared-network airport {  
    subnet 10.0.0.0 netmask 255.255.255.0 {  
    option routers 10.0.0.1;  
    option subnet-mask 255.255.255.0;  
    pool { range 10.0.0.10 10.0.0.20; }  
    }  
    }  
    #start dhcpd4  
    [jmorgan@arch-dopey ~]$ sudo systemctl start dhcpd4  

Launch wireshark, and watch traffic from the interface you are
forwarding from(enp7s0 for me).

Enjoy
