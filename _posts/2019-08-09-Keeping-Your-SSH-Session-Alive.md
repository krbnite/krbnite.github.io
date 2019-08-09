---
title: Keeping Your SSH Session Alive
layout: post
tags: unix easi
---

Strap a wearable on your wrist.  Your other wrist.  Your finger.  Transmit the data through 
a pipeline ending in an S3 bucket on AWS.  Now, SSH into an GPU-powered EC2 instance because
we're about to flex the power of a recurrent + convolutional deep neural network hybrid on
this sucka.

But first, where's that reference you were reading... And why isn't this GPU thing working?  Google
it...  Ok, ok -- I see.  And -- wtf?  The session timed out?

Log back in, set everything back up, start reading the reference... WTF?!  Session timed out again?!  This
never happened at my last job.  What gives? (Raise fists to sky!)

Sound like your life?  Ok, maybe it was a bit too specific, but SSH session timeouts can harm you
and your productivity anyway!  Read on for a quick tip or two.

------------------------------------------------------

This solution did not work, but is seems to be recommended quite a bit for this situation:

On the client side (e.g., your laptop) in the `/etc/ssh/ssh_config` file:

```
# Keep SSH session active (was getting logged out after 5 mins
# of inactivity)
#  -- [1] send null packet to server every minute to keep alive
ServerAliveInterval 60
#  -- [2] try up to 10x if response is not received
ServerAliveCountMax 10
```

This was the next solution I was recommended.  It also didn't work and I was told it is a less
secure solution.  

On your server (e.g., your AWS EC2 instance) in the `/etc/ssh/ssh_config` file:
```
#  Send null packet to client every 2 mins to keep alive
ClientAliveInterval 120
# Try up to 20x if response is not received
ClientAliveCountMax 20
```

Finally, the solution that worked for me: change your `TMOUT` environment variable.  So on 
your EC2 instance (assuming Linux Ubuntu), in your `.bashrc` file:

```
# For a 2-hr timeout
export TMOUT=7200 
# Or for infinite time
export TMOUT=
```

# Some References
* [Changing the Auto-Logout Timeout in SSH](http://www.adercon.com/ac/node/39)
* [How To Auto Logout Inactive Users After A Period Of Time In Linux](https://www.ostechnix.com/auto-logout-inactive-users-period-time-linux/)
* [How to prevent SSH from timing out](https://www.itworld.com/article/2701512/how-to-prevent-ssh-from-timing-out.html)

