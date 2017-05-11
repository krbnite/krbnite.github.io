To use a Jupyter Notebook or TensorBoard on AWS is straightforward on a personal account. 
It's a little trickier if the AWS account you're using is a work account with restricted permissions, and so on.
First, I'll just lay out how I've done this on my personal account.  Then, I will go over some of the 
additional steps that were necessary for my work account.

# Personal Account
I've been participating in Udacity's [Deep Learning NanoDegree Foundation](https://www.udacity.com/course/deep-learning-nanodegree-foundation--nd101)
since January.  For extra performance, we were given $100 worth of AWS credits so that we could run our deep neural
nets on a GPU.  

Have you ever trained on a GPU after only ever training on your laptop's CPU?  Richard Feynman is quoted as saying, 
"Physics is to mathematics like sex is to masturbation."  Basically, this is what a GPU is to deep learning.  Sure, 
you will use a CPU afterwards, and it will still be ... nice.  But only when a GPU is not around!

I thought about purchasing my own GPU/hardware and all that, but for what I've done so far, it seems 
cloud computing is the best route.  This seems especially true considering that any hardware I buy is
likely to become old-fashioned, whereas I suspect I can keep up-to-date in the cloud.

Anyway! Look at me rambling.  That's not why we're here!  Without further ado:

## How to Access a Jupyter Notebook and TensorBoard from a Python Session Launch on your Personal EC2 Instance

I simply launched a g2.2xlarge instance using Udacity's AMI.  To connect to the instance,
just note the (public) IP address it was assigned and SSH into it.  If using Udacity's AMI, make 
sure to get into the proper Conda environment, then start up your Jupyter Notebook.  
To actually see your notebook, simply enter the IP and port number in the browser.  
(If you're not using the Udacity AMI, you might need to copy-and-paste the URL token
provided in the remote shell when called on Jupyter.)

```{bash}
# In the Shell
ssh userName@Ip.Address
# In the Remote Shell
cd <yourProjectDir>
source activate <yourDeepLearningEnv>
# Start up TensorBoard in Background (&: take note of PID to kill later)
mkdir -p tfLogs
tensorboard --logdir tfLogs &
# Start up JNB in Background (&: take note of PID to kill later)
jupyter notebook --no-browser --port=portNum &   # e.g., port=8887 (default: 8888)
# In your Browser:  
#   -- JNB:  Ip.Address:portNum
#   --  TB:  Ip.Address:6006
```
Note that the JNB portNum defaults to 8888, so it is actually unnecessary to specify 
if you are accessing the EC2 instance as a single user working on a single project.
However, if your project gets a little more complex, now you know!

### Wrapping Up Your Work Day
When you're done working for the day, you should kill the various processes that are running.

```{bash}
kill jnbPID
kill tbPID
# exit remote shell 
exit
```


# Work Account
This was trickier for me.  I first had to convince my boss that the GPU would 
decrease our model's runtimes by 3-5x, which would allow me to more quickly 
explore the feature, parameter, and hyperparameter spaces of our models, and
afford us the time to be more experimental and hypothesis-drive, etc.

It was an easy sell, which in regular life would translate into simply logging
into an AWS account an setting it up.  But not so in corporate life.  Setting up
this instance is a series of discussions and negotiations with multiple parties.
And once set up, it was about why X didn't work, or [why I don't have permission
to do Y](http://stackoverflow.com/questions/22955682/ec2-instance-on-aws-apt-get-not-working).  
More discussion.  More waiting.  And so on.

Anyway, as one might expect, the EC2 instance I use at work is not on a public
IP, but a private IP.  This adds an additional layer or two of complexity to
setting up your JNB and TensorBoard.  
On a public IP, you can just type into your browser <public IP>:8888 to bring up the JNB running on your EC2 instance.  
However, a private IP requires proper SSH authentication, so in this case you must create what's called an 
"[SSH tunnel](https://coderwall.com/p/ohk6cg/remote-access-to-ipython-notebooks-via-ssh)."

To make things easy on myself, I had requested that the service provider install the Udacity AMI,
which they did, but which did not work properly for me.  This sent me down a rabbit hole
of figuring out if CUDA was installed properly, and is fodder for another post.  Suffice it to say, 
I did eventually figure it out.

## How to Access a Jupyter Notebook and TensorBoard from a Python Session Launch on an EC2 Instance with Private IP

Note that this advice directly applies to using Mac OS X, but much of it translates over to a PC.
(The process will be a little different, e.g., you will use the .ppk instead of the .pem file.  
Read more about it [here](https://aws.amazon.com/blogs/security/securely-connect-to-linux-instances-running-in-a-private-amazon-vpc/).)

```{bash}
# In the Shell
# Obtain a .pem file and put it into ~/.ssh/
mv fileName.pem ~/.ssh/
# Restrict permissions on .pem file (next step will be denied otherwise)
chmod 600 ~/.ssh/wwe.pem  
# Add .pem file to SSH agent
ssh-add –K ~/.ssh/wwe.pem
# Create SSH tunnel to JNB port (&: put in background; take note of PID to kill later)
#  -- note I explicitly use a non-default port, which (I think) is necessary if multiple
#     users will be accessing the same instance and using a JNB
ssh -N -L localhost:8887:localhost:8887 userName@Ip.Address &
# Create SSH tunnel to TensorBoard port (&: put in background; take note of PID to kill later)
ssh -N -L localhost:6006:localhost:6006 userName@Ip.Address &
# SSH into Linux Instance
ssh userName@Ip.Address
# In the Remote Shell
cd <yourProjectDir>
source activate <yourDeepLearningEnv>
# Start up TensorBoard in Background (&: take note of PID to kill later)
mkdir -p tfLogs
tensorboard --logdir tfLogs & 
# Start up JNB in Background (&: take note of PID to kill later)
jupyter notebook --no-browser --port=8887 & 
# In your Browser:  
#   -- JNB:  Copy-and-paste the provided URL / token from the JNB start-up output
#   --  TB:  Ip.Address:6006
```
### The JNB Token Number
When you start up the Jupyter Notebook in the remote window, it will provide you with the token number.  
This is a security measure.  If you don't include the token number, you will be locked out of the JNB in your browser.  
(Read more about [token authentication](https://github.com/jupyter/notebook/blob/master/docs/source/security.rst).)

### Wrapping Up Your Work Day
When you're done working for the day, you should kill the various processes that are running.

```{bash}
kill jnbPID
kill tbPID
# exit remote shell and kill ssh tunnels
exit
kill jnbTunnelPID
kill tbTunnelPID
```
 
