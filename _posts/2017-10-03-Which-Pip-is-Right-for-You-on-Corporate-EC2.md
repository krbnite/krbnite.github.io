---
title: Which Pip is Right for You on Corporate EC2?
layout: post
---

I ran into this annoying issue before while trying to 
[install TensorFlow](https://krbnite.github.io/Make-an-Ailing-AWS-EC2-Tensorial-and-Serpentine-Once-Again/)
on my work-provided [g2.2xlarge](https://aws.amazon.com/ec2/instance-types/).  

Here it is in a nutshell:
* I SSH into a Linux Ubuntu EC2 instance
* On this instance, Anaconda is installed
* conda usually installs things fine
* some python packages need pip
* a failure occurs due to lack of permission
* sudo pip (or sudo pip -H) appears to install the package
* open up python or ipython: package does not exist

For TensorFlow, I was able to use conda and get things working again.  But for selenium, I need pip.


#### Pip installation fails occurs due to lack of permission
<figure>
<img src="/images/ec2-selenium-failure.png" width="600">
</figure>

#### Will sudo help?
Not in my case: sudo's success was deceptive. Apparently selenium was installed, yet when
tried to run some selenium python code, it would fail.

#### WTF is going on?
Turned out, the **what* had a lot to do with the **which**.  

1. I ran `sudo pip install selenium` and was told I should probably be using sudo's `-H` flag
2. So I ran `sudo -H pip install selenium`
  - I was told that selenium was already installed in `/usr/local/lib/python2.7/dist-packages`
  - Wait a second!  I use Python3, so maybe I should be using pip3 (even though that distinction usually doesn't matter with Anaconda)
3. Worth a try: `sudo -H pip3 install selenium`
  - When I tried running a program using selenium, I was still told that there was no module named selenium

Clearly PIP and Anaconda weren't talking to each other, so I which'd 'em:
```python
which python     # /home/ubuntu/anaconda/bin/python
which ipython    # /home/ubuntu/anaconda/bin/ipython
which pip        # /home/ubuntu/.local/bin/pip
which pip3       # /usr/bin/pip3
```

Those pips weren't looking too Anaconda-compliant, so I got rid of 'em and conda-installed pip:
```python
pip uninstall pip
conda install pip
```

Now this is very near the end of the case, but not quite.  When I tried to install selenium,
I was told pip did not exist:
```
pip install selenium
-bash: /home/ubuntu/.local/bin/pip: No such file or directory
```

Well, no shit -- I just obliterated that pip.  Where did my conda-installed pip wander off to? Oddly, 
I figured that out by whiching pip again:
```
which pip  # /home/ubuntu/anaconda/bin/pip
```

So it exists, I know where it is, and I know how to use it:
```
/home/ubuntu/anaconda/bin/pip install selenium
```

Frackin' worked.  Job done.  Why does which pip correctly point to Anaconda's pip despite the fact that calling `pip`
directly gives us an error?  This likely has to do with broken links or something.  However, the job is done:  I successfully
installed selenium, and know how to approach this problem in the future.  So for now, I'm done too!

