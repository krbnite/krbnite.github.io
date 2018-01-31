---
title: Conda Quick Guide
layout: post
---

We will be using Python in the Deep Learning Nanodegree, specifically the Anaconda
distribution.  Conda is a package management and virtual environment tool for creating
and managing various Python environments.


## Create a New Environment
```
conda create -n <env_name> python=<vNum> <pkg1> <pkg2> ... <pkgN>
```

NOTE: it's a good practice to have two "default" environments on your system:
one for Python 2.7.x and one for Python 3.y.z  (as of now, 3.6.z).  That said,
any new project you begin in Python should be in Python3.


## Packages
### List all packages in your environment
```
conda list
```

### Install new packages
```
conda install <pckgNm1> <pckNm2> ...
```

NOTE:  dependencies are automatically installed.

### Install specific version of new package
```
conda install <pckNm>=<vNum>
```

### Uninstall a package
```
conda remove <pckNm>
```

### Update/Upgrade all packages
Update and upgrade are synonyms as far as conda is concerned. The following
two commands do the same thing.
```
conda update --all
conda upgrade --all
```


### Search Conda's Database for a Package 
This is helpful if you're unsure of a package name, but "kinda remember."
```
conda search <search_term>
```

## Conda Environments
### Enter an environment
```
# OSX/Linux:
source activate <envNm>
# Windows
activate <envNm>     
```
Note that on Windows, this doesn't work in PowerShell.  Instead, I found that
you must use old cmd prompt or the one provided by Anaconda.

### Leave an environment 
To "leave" an environment is to head back to default Python (called "root").
```
# OSX/Linux
source deactivate
# Windows
deactivate
```

### List All Your Anaconda Environments
Forgot what environments you have?  Forgot an environment name?  That's Ok.
```
conda env list
```

### Remove an environment you no longer need
```
conda env remove -n <envNm>
```

### Create an Environment YAML File
This allows fo easily reusable and reproducible conda environments, e.g.,
for sharing code on GitHub .
```
conda env export > myEnvironment.yaml
```
It is recommend that, if sharing on GitHub, one also include the pip version of
the environment as well for those not using Anaconda:
```
pip freeze
```

### Create an Environment from Existing YAML Environment File
```
conda env create -f coolNewEnvironment.yaml
```
