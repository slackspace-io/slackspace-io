Title: onConnect updated to handle additional trigger.
Date: 2013-04-01 15:39
Author: Jacob Morgan
Category: Code
Tags: automation, python
Slug: onconnect-updated-to-handle-additional-trigger

I expanded onConnect, you can check out the current version [here][]. I
plan to add more triggers, but for now I've added a File trigger.

The configuration file now supports a 'File:$name' section, that you
then define a fine to monitor, expected contents of the file, and
whether you want to run a command when that file does or does not match
the expected value. Only acting when the value changes, though.

But why you might ask? Well, after reading [Bumblebee on Arch Wiki][] I
wanted to toggle Nvidia vs Intel on my XPS 15 whenever I moved to/from
battery or A/C power. I was initially going to hardcode onConnect to
handle this, but that seemed limiting.

Instead I have a framework for other triggers, and simply had to point
onConnect to monitor */sys/class/power_supply/ACAD/online* file which
will change depending on my power source.

Here it is changing from AC power to battery, you can also see the
increase in battery time using acpi.  

    :::bash  
    #Unplugging the power cable  
    DEBUG:root:State changed for File:onACPower  
    DEBUG:root:New state matches configuration File:onACPower, running
    actionFalse Cmd echo OFF > /proc/acpi/bbswitch  
    DEBUG:root:Found File:onBatteryPower  
    DEBUG:root:Loading Configuration for File:onBatteryPower  
    DEBUG:root:Looking up prior state for File:onBatteryPower  
    DEBUG:root:Prior state for File:onBatteryPower is 1  
    DEBUG:root:State changed for File:onBatteryPower  
    DEBUG:root:New state matches configuration File:onBatteryPower, running
    actionTrue Cmd echo OFF > /proc/acpi/bbswitch  
    #watching acpi/bbswitch status in another window. Before/After the
    configuration change above  
    #plugged in AC power with bbswitch 'ON'  
    [jpyth@arch-jpyth jpyth]# cat /proc/acpi/bbswitch ; acpi  
    0000:01:00.0 ON  
    Battery 0: Unknown, 99%  
    #unplugged, before the next run occured(13 second window).  
    [jpyth@arch-jpyth jpyth]# cat /proc/acpi/bbswitch ; acpi  
    0000:01:00.0 ON  
    Battery 0: Discharging, 96%, 02:19:09 remaining  
    #after the above run competes. bbswitch is 'OFF', and battery time
    increased.  
    [jpyth@arch-jpyth jpyth]# cat /proc/acpi/bbswitch ; acpi  
    0000:01:00.0 OFF  
    Battery 0: Discharging, 96%, 03:37:31 remaining  
    #behaves exactly as expected when plugging power back in. The above is
    a set of redundant configs, only to show the True/False case possible
    with the configuration. I'd normally only have one.  

I can now tie in the g13 daemon quite easily, and monitor the log file
for error to stop the daemon upon disconnect and also start the daemon
when the device is connected automatically.

  [here]: https://github.com/jpyth/onConnect
  [Bumblebee on Arch Wiki]: https://wiki.archlinux.org/index.php/Bumblebee
