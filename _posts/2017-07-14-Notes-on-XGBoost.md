---
layout: post
title: Notes on XGBoost
tag: machine-learning wwe
---

So you're trying to install XGBoost?

It is not as simple as "sudo pip install xgboost," which gave me an error about a python egg!

Advice on the internet said:
```
# 1. Upgrade Pip
pip install --upgrade pip  
pip install xgboost  # ERROR

# 2. Upgrade Pip 2x
pip install --upgrade pip  
pip install --upgrade pip  
pip install xgboost # didn't work

# 3. Upgrade setuptools
pip install --upgrade setuptools
pip install xgboost # didn't work

# 4. Upgrade ez_setup
pip install --upgrade ez_setup
pip install xgboost # didn't work

# 5. Use upgraded ez_setup to upgrade setuptools
easy_install -U setuptools
pip install xgboost # didn't work

# 6. Make sure gcc is up-to-date
brew install gcc --without-multilib   # Was already up-to-date
pip install xgboost # didn't work

# 7. Try using conda...?
conda install xgboost
#...package not found...

# 8. Clone xgboost from GitHub and build
git clone https://github.com/dmlc/xgboost
cd xgboost
./build.sh
# Failed... Errors
sudo ./build.sh
# Failed... Errors

# 9. Use make inside xgboost directory?
make # Fail!

# 10. Ah, use this [other command](https://stackoverflow.com/questions/32838959/python-xgboost-on-mac-install) inside xgboost directory
cp make/minimum.mk ./config.mk; make -j4 # NOPE.
```

### At one point, it was Time to [Read the Docs](https://xgboost.readthedocs.io/en/latest/build.html)

Ah, the "git clone" advice was good, but old.  Nowadays, one must use the recursive flag.
```
git clone --recursive https://github.com/dmlc/xgboost
cd xgboost; cp make/minimum.mk ./config.mk; make -j4
# Fail...... errorerror: error: errorunsupported option '-fopenmp'
```

### Ok... wtf is [openmp](https://en.wikipedia.org/wiki/OpenMP) anyway?  
It's true: to [Configure OpenMP on a Mac](https://shawnliu.me/post/configuring-openmp-and-mpi-on-mac/) one 
needs an up-to-date gcc.  However, what I didn't do before was *reinstall* gcc:
```
brew reinstall gcc --without-multilib
```

According to the tutorial, I should now be able to compile programs w/ OpenMP support like so:
```
gcc-6 -fopenmp  # gcc-7 on my system
```

Importantly, my hope is that my computer will not give an error saying the fopenmp flag is not an supported option.
```
cp make/config.mk ./config.mk; make -j4
# Drum roll.........
# Nope :-(
```

The following note on the Docs page was key:
>"NOTE: If you use OSX El Capitan, brew installs gcc the latest version gcc-6. So you may need to modify 
> Makefile#L46 and change gcc-5 to gcc-6. After that change gcc-5/g++-5 to gcc-6/g++-6 in make/config.mk 
> then build using the following commands"

No matter what "which gcc" kept pointing to the non-brewed version in /usr/bin. That's b/c HomeBrew
purposely names its gcc's w/ the leading version number to avoid confusion (e.g., gcc-7).  However,
I vimmed into the config.mk file and saw that, by default, it used the system gcc.  I had to set
gcc and g++ to gcc-7 and g++-7, respectively.

```
vim make/config.mk  
# look for commented out section out top ("choice of compile")
# 1. uncomment "export CC = gcc" and change gcc to gcc-7
# 2. uncomment "export CXX = g++" and change g++ to g++-7
```

Then magic happened!  No errors.  Things happened.  It built!

The next step on the Docs page was to install the Python library:
```
cd python-package
sudo python setup.py install
```

That worked too! :-)

You might have to make a PYTHONPATH environment variable to export in your .bash_profile... 
But I didn't have to... So not sure.

### Btw, one might also wonder: [wtf is MPI](https://en.wikipedia.org/wiki/Message_Passing_Interface)?
See the same tutorial if you want to install MPI...
```
brew install openmpi --build-from-source --cc=gcc-6
```

### Some References
* Read the Docs: [XGBoost](https://xgboost.readthedocs.io/en/latest/build.html)
* Machine Learning Mastery: [XGBoost Python Mini Course](http://machinelearningmastery.com/xgboost-python-mini-course/)
* Machine Learning Mastery: [Gentle Introduction to XGBoost](https://machinelearningmastery.com/gentle-introduction-xgboost-applied-machine-learning/)
