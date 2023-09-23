Title: Spell Check
Date: 2013-04-22 12:10
Author: Jacob Morgan
Category: Operations
Tags: configuration
Slug: spell-check

Simple, but I'm sure many people go around running Arch(and other
distros) without spell check when using firefox/webmail/etc. Simple to
fix, if you know.

I use hunspell, so I installed the English dictionary to go along with
it.  

    :::bash
    [jmorgan@arch-jm ~]$ sudo pacman -S hunspell-en  
    resolving dependencies...  
    looking for inter-conflicts...
    
    Packages (1): hunspell-en-7.1-2
    
    Total Download Size: 0.36 MiB  
    Total Installed Size: 1.65 MiB
    
    :: Proceed with installation? [Y/n] y  
    :: Retrieving packages ...  
    hunspell-en-7.1-2-any 364.8 KiB 443K/s 00:01
    [######################] 100%  
    (1/1) checking keys in keyring
    [######################] 100%  
    (1/1) checking package integrity
    [######################] 100%  
    (1/1) loading package files
    [######################] 100%  
    (1/1) checking for file conflicts
    [######################] 100%  
    (1/1) checking available disk space
    [######################] 100%  
    (1/1) installing hunspell-en
    [######################] 100%  
    Optional dependencies for hunspell-en  
    hunspell: the spell checking libraries and apps [installed]  
    [jmorgan@arch-jm ~]$  
