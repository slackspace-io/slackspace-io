Title: Client/Server Python Scripts
Date: 2013-07-22 12:26
Author: Jacob Morgan
Category: Code
Tags: python, triage
Slug: clientserver-python-scripts

I started this site with the intent to do a weekly post, however I've
found myself in Sweden for the past 8+ weeks for work.(There are worse
places to spend your summer;). Sorry for lack of updates;x

There are 1000 different ways to triage network issues, here is one
tool. A simple python server listening on a particular port, and
printing out the details of what the client sent and a client to send
said datas.

Server:  

    :::python
    [jmorgan@arch-dopey ~]$ cat server.py  
    #!/usr/bin/python2
    
    import socket  
    from datetime import datetime
    
    s = socket.socket()  
    host = socket.gethostname()  
    port = 1337  
    s.bind((host, port))
    
    s.listen(5)  
    while True:  
    c, addr = s.accept()  
    sockS= c.recv(3000).strip('\n')  
    if sockS:  
    logF = open('rLog', 'a')  
    dt= str(datetime.now())  
    wee="%s %s \n" % (sockS, dt)  
    logF.write(wee)  
    logF.close()  
    sockS=None  
    c.close()  
Client:  

    :::python
    [jmorgan@arch-dopey ~]$ cat tcpSend.py  
    #!/usr/bin/python2  
    import sys  
    import socket  
    from datetime import datetime  
    ip = sys.argv[1]  
    port = int(sys.argv[2])  
    mCount = int(sys.argv[3])  
    print "%s:%s %s packets" % (ip, port,mCount)  
    count=0  
    while count \<= mCount:  
    logF = open('sLog', 'a')  
    dt= str(datetime.now())  
    msg="%s %s" % (str(count), dt)  
    try:  
    sock = socket.socket(socket.AF_INET, # Internet  
    socket.SOCK_STREAM)  
    sock.connect((ip, port))  
    sock.sendto(msg, (ip, port))  
    logF.write("Success: %s\n" % (msg))  
    sock.close()  
    except:  
    dtE= str(datetime.now())  
    logF.write("Fail:%s Start: %s End: %s\n" % (str(count), dt, dtE))  
    count +=1  
    logF.close()  
    [jmorgan@arch-dopey ~]$  
    
Usage: 
 
    :::bash  
    #One terminal  
    [jmorgan@arch-dopey ~]$ sudo ./server.py  
    #Another terminal  
    [jmorgan@arch-dopey ~]$ ./tcpSend.py localhost 1337 3  
    localhost:1337 3 packets  
    [jmorgan@arch-dopey ~]$ cat sLog  
    Success: 0 2013-07-22 21:17:12.124353  
    Success: 1 2013-07-22 21:17:12.124862  
    Success: 2 2013-07-22 21:17:12.125124  
    Success: 3 2013-07-22 21:17:12.125321  
    [jmorgan@arch-dopey ~]$ cat rLog  
    0 2013-07-22 21:17:12.124353 2013-07-22 21:17:12.124854  
    1 2013-07-22 21:17:12.124862 2013-07-22 21:17:12.125148  
    2 2013-07-22 21:17:12.125124 2013-07-22 21:17:12.125335  
    3 2013-07-22 21:17:12.125321 2013-07-22 21:17:12.125485  
    [jmorgan@arch-dopey ~]$  

The client sends the timestamp when it sends the packet, and the server prints that along with when it received the packet. Works well to compare latency, dropped packets, etc over a network. Nothing fancy, just a quick and dirty script written under fire to triage an issue. In my situation, I would have the VIP quit responding at times so the Fail line let me know how often that happened for say 10,000 or 100,000 packets, as well the amount of time it took to send that number of packets between zones.
