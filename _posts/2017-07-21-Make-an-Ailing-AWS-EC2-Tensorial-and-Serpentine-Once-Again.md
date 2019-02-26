---
layout: post
title: Make an Ailing AWS EC2 Tensorial and Serpentine Once Again
tags: aws python tensor-flow unix-tools wwe
---

It could happen to any of us: you SSH into your EC2 and, "Wtf?"  Nothing is working right.  
Python doesn't recognize libraries you've always used.  You can't source activate into another Conda environment.  
"What is going on?!"

Answer: I don't know. A bit choked on a byte.  Something.  Doesn't matter now: the devastation is real!  
Your job in this moment is to remain calm and get things working again.  

## 1. Remove the ailing Anaconda distribution on your Linux EC2
```
rm -rf anaconda
```
## 2. Get new distro (Anaconda or MiniConda)
```
curl https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -o ~/miniconda3.sh
bash ~/miniconda3.sh -bfp ~/anaconda
echo  'export PATH="/home/ubuntu/anaconda/bin:$PATH"' >> .bashrc
```
## 3. Install a bunch of Python packages
This has to be done, whether you just do it in your default conda environment, or
if you have several custom environments you have to build.
```
pip install jupyter
conda install numpy pandas scikit-learn matplotlib seaborn psycopg2 sqlalchemy requests selenium rpy2 pillow
```

## 4. What about TensorFlow?
This is what I had some problems with... In several cases, it would appear the TF was installed just fine,
but then would not be recognized inside python, or some other type of error would be issued.

For example, DO NOT USE SUDO to install TF.  Though I can hypothesize, I don't really know why 
this is the case (if you do, please leave it in the comments).

Oddly, any way I pip-installed, an error would crop up somewhere, somehow.  I followed the advice of many
StackOverflow authors, none of which solved my problem with installing TF.  I even followed the advice
on TF's website.  Still didn't work.  (I'm not including any of these attempts to avoid cluttering the 
page w/ "solutions" that did not work for me.)  

What worked is a simple conda installation:
```
conda install tensorflow-gpu
```

This should have been the first thing I tried, but the TF website showed several ways, none of 
which involved conda.  Oftentimes a package must be pip-installed when it's not available as a
conda installation... I just figured this was the case.  

Anyway, by the time you're reading this,
the TF website might already give this advice.  Or, worse, this advice might not work the 
next time this issue occurs.  

Happy times!  Have a nice day :-p
