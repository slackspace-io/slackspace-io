Title: 'psg'
Date: 2013-03-27 04:26
Author: Jacob Morgan
Category: Operations
Tags: configuration
Slug: psg

Introduced to this years ago by a colleague, but it's still my favorite
little alias.
  
    :::bash
    #Add to ~/.bashrc  
    alias psg='ps aux | grep -v grep | grep -i'  
    #Source the updated bashrc(or relog)  
    source ~/.bashrc  
    #I prefer to not be case sensitive, example:  
    [jpyth@arch-jpyth ~]$ psg fire  
    jpyth 16131 12.7 3.7 1463388 607748 ? Sl Mar25 367:33
    /usr/lib/firefox/firefox  
    [jpyth@arch-jpyth ~]$ psg FiRe  
    jpyth 16131 12.7 3.7 1463388 607748 ? Sl Mar25 367:33
    /usr/lib/firefox/firefox  
    [jpyth@arch-jpyth ~]$  
