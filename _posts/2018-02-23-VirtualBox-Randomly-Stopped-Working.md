---
title: VirtualBox Randomly Stopped Working
layout: post
tags: virtual-environments wwe
---

I have no idea why, but yesterday when I tried opening VirtualBox to play around, my MacBook Pro
told me that it failed to open.  Huh?  Whatever, let me reboot and try again... Failed to open!  Ok, ok -- let
me google it this.  What do I write? "VirtualBox won't open." Too general... Oh, I know!  Try opening it
in Terminal.

```bash
cd /Applications/
open VirtualBox.app/
  LSOpenURLsWithRole() failed with error -10810 for the file /Applications/VirtualBox.app.
```

Sweet, at least it gave me something specific to google. 

Hmm, seems like everyone thinks things like this could be a permissions problem.  I will check to make sure
my Mac's security settings allow apps from sources other than Apple.  Yes, it does.  Well, let's look
in Terminal:

```bash
ls -l | grep VirtualBox
  drwxr-xr-x   3 krbn  someNumber  102 Feb 13 13:19 VirtualBox.app
```

Looks like everyone has permission to read and execute... That group looks a little weird, so maybe that's
the problem.  

```bash
# Attempt 1: there is an admin group; try that
sudo chgrp -R admin VirtualBox.app/
open VirtualBox.app/
  LSOpenURLsWithRole() failed with error -10810 for the file /Applications/VirtualBox.app.
  
# Attempt 2: wheel
sudo chgrp -R wheel VirtualBox.app/
open VirtualBox.app/
  LSOpenURLsWithRole() failed with error -10810 for the file /Applications/VirtualBox.app.
  
# Ok... Change it back and try something else
sudo chgrp -R someNumber VirtualBox.app/
```

The other solution to similar issues was to reinstall VirtualBox.  The first time I installed VirtualBox, I
did so manually...but I wonder if you could just use HomeBrew?  One quick google later: yes, you can.  

```bash
brew cask install virtualbox
  ==> Satisfying dependencies
  ==> Downloading http://download.virtualbox.org/virtualbox/5.2.6/VirtualBox-5.2.6-120293-OSX.dmg
  ######################################################################## 100.0%
  ==> Verifying checksum for Cask virtualbox
  ==> Installing Cask virtualbox
  ==> Running installer for virtualbox; your password may be necessary.
  ==> Package installers may write to any location; options such as --appdir are ignored.
  ==> installer: Package name is Oracle VM VirtualBox
  ==> installer: Upgrading at base path /
  ==> installer: The upgrade was successful.
  🍺  virtualbox was successfully installed!
```

Nice! Let's take a look.

```bash
open VirtualBox.app
```

Success! And my Ubuntu VirtualBox is still there: double win!

Side Note: I noticed that the user and group are different when installing with HomeBrew.

```bash
ls -l | grep VirtualBox
  drwxr-xr-x   3 root    admin                 102 Feb 23 09:48 VirtualBox.app
```
