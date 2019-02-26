---
title: Connecting to a SAMBA Server from Mac and Ubuntu
layout: post
tags: unix-tools wwe
---

Maybe the IT department already set things up, but mounting a samba shared drive on my 
MacBook Pro work computer was super simple.

```
mkdir sharedDirectory
mount_smbfs '//username:password@SambaServerIP/sharedDirectory/' sharedDirectory
```

However, I don't really need to know how to do this... I need to know how to connect to that
shared directory while on my AWS EC2 Linux Ubuntu instance.  Alas, this has not proved to 
be as simple.

----------------------------------------------------------------

Currently, I'm following the information provided in this tutorial on 
[www.linuceum.com](http://www.linuceum.com/Server/srvSambaInstall.php).  It's been helpful,
but I still haven't figure things out...

```
sudo apt-get install samba
```

You should now have some samba- and NetBIOS-related daemon processes running:
```
ps -ef | grep smbd
ps -ef | grep nmbd
```

--------------------------------------------------------------
To stop/start a daemon:
```
# Stop smbd
sudo service smbd stop
# Check
ps -ef | grep smbd
# Start smbd
sudo service smbd start
```

----------------------------------------------------------
You should also now have an /etc/samba/ directory, inside of which you can find smb.conf.
To edit this file, first stop the smbd daemon.  Also, for sanity's sake, create a backup of
the file before editing.
```
sudo service smbd stop
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.backup
```

Find more information about editing the smb.conf file [here](http://www.linuceum.com/Server/srvSambaConfig.php).

----------------------------------------------------------
Whoa...... This stuff is starting to take too long....

Maybe this?
http://www.practicalreason.net/item/31-mounting-network-locations-on-linux-using-samba

```
sudo apt-get install smbclient
smbclient -L sambaServerIpOrName -U userName
```

http://linuxcourse.rutgers.edu/lessons/Samba/smbfs.php

Seems like I'm so close but so faaaaaaaaaaaaaaaaaaaaar...
