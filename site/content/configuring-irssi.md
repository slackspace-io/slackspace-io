Title: Configuring irssi
Date: 2013-03-25 21:17
Author: Jacob Morgan
Category: Operations
Tags: configuration, automation, scripting, logrotate
Slug: configuring-irssi

autoconnect:  

    :::bash  
    #server section of ~/.irssi/config  
    servers = (  
    {  
    address = "$hostname";  
    chatnet = "IRCnet";  
    port = "6667";  
    autoconnect = "true";  
    password = "$user:$pass";  
    }  
    );
    

irssi logging:  

    :::bash  
    #Create directory for logs  
    #Large storage drive == /storage/  
    sudo ln -s /storage/irssi /var/log/  
    #You'll probably want to own these files or atleast rw access.  
    chown -R $owner:$group /storage/irrsi  
    :::bash  
    #in irssi run:  
    /SET autolog ON  
    /SET autolog_path /var/log/irssi/$tag/$0.log  
    /save  

logrotate:  

    :::bash  
    #Contents of /etc/logrotate.d/irssi  
    /var/log/irssi/*/*log {  
    copytruncate  
    compress  
    notifempty  
    missingok  
    }  

[irssi-libnotify-git][] from Arch User Repository ([AUR][]):  

    :::bash  
    #For xfce4  
    sudo pacman -S xfce4-notifyd  
    #Get package from AUR  
    wget
    https://aur.archlinux.org/packages/ir/irssi-libnotify-git/irssi-libnotify-git.tar.gz  
    Extract && cd  
    tar -xvf irssi-libnotify-git.tar.gz  
    cd irssi-libnotify-git  
    #create package - Install missing deps  
    makepkg || {  
    #missing deps  
    sudo pacman -S python2-gobject perl-html-parser  
    }  
    makepkg  
    #install final package  
    sudo pacman -U irssi-libnotify-git*.pkg.tar.xz  
    #create directories if missing && symlink installed script for irssi
    to use.  
    mkdir -p ~/.irssi/scripts/autorun && ln -s
    /usr/share/irssi/scripts/notify.pl ~/.irssi/scripts/autorun/  
    #dep for notify-listener.py  
    sudo pacman -S python2-dbus  
    #start listener -- I do this when launching irc. You could start it
    with systemd/init too.  
    nohup notify-listener.py &  

Simple script to grab current or start a new irssi:  

    :::bash  
    #!/bin/bash
    
    screenN="irssi"  
    notify=\`which notify-listener.py\`  
    irc=\`which irssi\`
    
    ps aux | grep "$notify" | grep -v grep &>/dev/null || {  
    nohup $notify &  
    }  
    ps aux | grep -i "$irc" | grep -v grep &>/dev/null || {  
    screen -S $screenN $irc  
    } && {  
    screen -list | grep "$screenN" | wc -l | grep "\^1$" &>/dev/null &&
    {  
    screen -D $screenN  
    screen -R $screenN  
    } || {  
    echo "You has too many/few irssis, check screen"  
    }  
    }
    

Enjoy

  [irssi-libnotify-git]: https://aur.archlinux.org/packages/irssi-libnotify-git/
  [AUR]: https://aur.archlinux.org/
