---
layout: post
title: Cookiecutter Data Science
tags: data-science python easi
---

Way back when circa 2012 I was heavily into R and got introduced to [ProjectTemplate](http://projecttemplate.net/getting_started.html), which is
an R package that allows you to begin data science projects in a similar way -- with a similar
directory structure.  It was very useful, and navigating projects became intuitive.  Again, 
when digging a bit into single-page web apps, I was introduced to this
idea of maintaining a level of homogeneity in the file/folder structure across projects.  And from my limited forays
into Django and Flask, I know a similar philosophy is adopted.  But what about various data engineering
and data science projects primarily developed in Python.  For my 
Python-oriented projects at WWE, I kind of came up with my own ideas on how
each project should be structured...but this changed over time as I worked on many distinct, but related
projects.  Part of this leaks into the concept of how Git repos should be organized (subtrees? submodules?)
for similar projects, but in this post
I'll stick with just deciding on a good project structure.

In my new role, I came into a team that uses `cookiecutter`, which is similar to ProjectTemplate.  Basically, you 
can create any kind of
cookiecutter template you want, or use a pre-existing one! The point is choose a structure that you and
your team will use again and again.  This will ultimately help you and your team to build an intuition for any 
and all projects going on -- no more time wasted on trying to figure out the structure of an teammate's 
project.  

Though you can make your own cookiecutter template, many are already available -- and are likely good enough
as is.  In the spirit of saving time, you might not want to spend too much time coming up with the perfect
template!  The template we use for various machine learning / data
science projects is the [data science template](https://drivendata.github.io/cookiecutter-data-science/),
available on [GitHub](https://github.com/drivendata/cookiecutter-data-science). It has a well thought out directory 
structure that allows you to seamlessly go from the data exploration
stage of a data science project (in the `notebooks` directory) to more production-ready versions (basically, by 
refactoring your exploratory code and distributing it throughout the the various subdirectories of the 
`src` directory).  

This project template defaults to the following (for more info, see: https://drivendata.github.io/cookiecutter-data-science/):

```
├── LICENSE
├── Makefile           <- Makefile with commands like `make data` or `make train`
├── README.md          <- The top-level README for developers using this project.
├── data
│   ├── external       <- Data from third party sources.
│   ├── interim        <- Intermediate data that has been transformed.
│   ├── processed      <- The final, canonical data sets for modeling.
│   └── raw            <- The original, immutable data dump.
│
├── docs               <- A default Sphinx project; see sphinx-doc.org for details
│
├── models             <- Trained and serialized models, model predictions, or model summaries
│
├── notebooks          <- Jupyter notebooks. Naming convention is a number (for ordering),
│                         the creator's initials, and a short `-` delimited description, e.g.
│                         `1.0-jqp-initial-data-exploration`.
│
├── references         <- Data dictionaries, manuals, and all other explanatory materials.
│
├── reports            <- Generated analysis as HTML, PDF, LaTeX, etc.
│   └── figures        <- Generated graphics and figures to be used in reporting
│
├── requirements.txt   <- The requirements file for reproducing the analysis environment, e.g.
│                         generated with `pip freeze > requirements.txt`
│
├── setup.py           <- Make this project pip installable with `pip install -e`
├── src                <- Source code for use in this project.
│   ├── __init__.py    <- Makes src a Python module
│   │
│   ├── data           <- Scripts to download or generate data
│   │   └── make_dataset.py
│   │
│   ├── features       <- Scripts to turn raw data into features for modeling
│   │   └── build_features.py
│   │
│   ├── models         <- Scripts to train models and then use trained models to make
│   │   │                 predictions
│   │   ├── predict_model.py
│   │   └── train_model.py
│   │
│   └── visualization  <- Scripts to create exploratory and results oriented visualizations
│       └── visualize.py
│
└── tox.ini            <- tox file with settings for running tox; see tox.testrun.org
```

There might be things in this template that you do not like or will never use.  That's fine -- you can
remove them or swap them out project by project, or modify the template itself and create a new
cookiecutter template ([your first cookiecutter](https://cookiecutter.readthedocs.io/en/latest/first_steps.html)).  But 
at the very least, it's a great first draft for developing your own template.  

Starting a new project from the cookiecutter-data-science template is easy:
```
# if you haven't used cookiecutter-data-science yet
cookiecutter https://github.com/drivendata/cookiecutter-data-science
```

After using this command the first time, the template is stored in `~/.cookiecutters`.  If you
use it again, you will be asked if you want to delete and redownload the template.  That's fine if
you have not modified the template file (actually, if you do modify the template, it's probably better to 
just save as a new template).  Mostly this is just an annoying extra step!  

So, for subsequent projects:
```
cookiecutter cookiecutter-data-science
```

You can look at what cookiecutter templates you have:
```
ls ~/.cookiecutters
```

For the data science template, you will go through a series of initialization questions, such
as choosing the project name.  Here is an example session:

* make a new, empty directory (so we can easily see what happens)
  - `mkdir tempDir; cd tempDir`
* project_name [project_name]:  `greatestProject`
  - this field defaults to `project_name`
  - you will find that the thing in square brackets often represents the default
  - to emphasize this, I called the project `greatestProject`, which will be the default for `repo_name` 
* repo_name [greatestProject]: `greatestProjectTribute`
  - see the default?!
  - I call it greatestProjectTribute so that it becomes obvious how these variables are used
    * for example, the directory that is created will be called `repo_name`, which may be different than `project_name`
    * `project_name` is used as the project name in the README.md file
* author_name [Your name (or your organization/company/team)]: `Krbn`
  - this is used in `setup.py`
* description [A short description of the project]: `This is not The Greatest Project in the World; no, this is just a tribute.`
    - this is used in `setup.py`
* Select open_source_license (1 - MIT; 2 - BSD-3-Clause; 3 - No license file) [1]: 1
  - this is used in `setup.py`
  - also, the chosen license is stored in top-level directory
* s3_bucket [[OPTIONAL] your-bucket-for-syncing-data (do not include 's3://')]: greatestProjectTributeBucket
  - stored in Makefile: `BUCKET = greatestProjectTributeBucket`
  - whatever S3 bucket name you specify, a data subdirectory will be assumed: `s3://$(BUCKET)/data/`
* aws_profile [default]:
  - if you do not specify a default AWS profile, it will use `~/.aws/config`
* Select python_interpreter (1 - python3; 2 - python) [1]:
  - defaults to python3


I described a bit where all this information gets stored.  Below, I show it in more detail.


#### Project Directory
```
# Show that we are still in tempDir (cool one liner)
pwd | rev | cut -d/ -f1 | rev
  tempDir

# Show that only one directory was made, called greatestProjectTribute
#  -- i.e., the project directory was named after the repo_name variable
ls
  greatestProjectTribute
```
  

#### Makefile
This is where you'll find the chosen S3 bucket info stored.  Confusingly, the `PROJECT_NAME` variable
in this file is what we defined as the `repo_name` during set up, which defaults to `project_name`, but
is not necessarily `project_name`. The `PROFILE` variable below is set to `default`, which tells the make
file to use the info in `~/.aws/config`.

```
head -n 12 Makefile 

  .PHONY: clean data lint requirements sync_data_to_s3 sync_data_from_s3

  #################################################################################
  # GLOBALS                                                                       #
  #################################################################################

  PROJECT_DIR := $(shell dirname $(realpath $(lastword $(MAKEFILE_LIST))))
  BUCKET = greatestProjectTributeBucket
  PROFILE = default
  PROJECT_NAME = greatestProjectTribute
  PYTHON_INTERPRETER = python3

```

#### setup.py
This is where author name and description are stored.
```
cat setup.py 
  from setuptools import find_packages, setup

  setup(
      name='src',
      packages=find_packages(),
      version='0.1.0',
      description='This is not The Greatest Project in the World; no, this is just a tribute.',
      author='Krbn',
      license='MIT',
  )
```

#### README.md
Here, you will find that whatever you specified `project_name` as, independent of what you
called `repo_name`.  My 2 cents is to always make `repo_name` the same as `project_name`, unless
for some reason this is not possible... Can't imagine why at the moment, but I'm sure someone somewhere
would tell me I'm an idiot if I made too strong of a statement :-p

```
head README.md 
  greatestProject
  ==============================

  This is not The Greatest Project in the World; no, this is just a tribute.

  Project Organization
  ------------

      ├── LICENSE
      ├── Makefile           <- Makefile with commands like `make data` or `make train`
```


---------------

# Mix in a Few More Reality Bits (Brain Storm)
Ok, so here is one way I'm using `cookiecutter` with Git.  I work on a highly exploratory partnership with
another group... In other words, there is nothing set in stone besides "let's try to do something together."  Given
this, there are ideas that become exploratory projects, which fall to the wayside for newer ideas, and so on.  Some of
the projects are not even code-driven or data science-y, much of which I am supposed to keep managed in Microsoft's 
SharePoint or OneDrive.  The code-y bits are supposed to be managed on GitLab.  And data?  No data in SharePoint or 
GitLab -- only on AWS.  So here is a situation where project organization comes to the forefront!  

Thankfully, the `Data Science Cookiecutter` already makes it so data is primarily stored on AWS, from which
you can sync the data to your local directory and erase when desired.  This works well with Git and the 
requirement to not host data on GitLab: just put the data directory into the .gitignore file.  Ignoring
OneDrive/SharePoint, one can quickly come up with a scheme, e.g.:

* Make Partnership folder
  - `mkdir partnership`
* Make it a Git repo
  - `cd partnership; git init`
* Create a corresponding repo on GitLab
  - pretend it can be found at: `https://gitlab.com/{user_name}/{partnership_project}`
* Connect the local git repo to the GitLab repo
  - `git remote add origin https://gitlab.com/{user_name}/{partnership_project}`
* Create various Cookiecutter directories for each relevant project
  - `cookiecutter cookiecutter-data-science`
  - fill out project name, etc
4. Use subproject naming scheme for Git commits (make history search easy)
  - e.g., `git add proj1/; git commit -m "proj1: add proj1"`

What gets tricky is having to use SharePoint/OneDrive: there is no .gitignore equivalent, despite folks asking
for this feature for years (just google something like ".gitignore equivalent in onedrive").  This means having
the data directory breaks the requirement of not ever having data hosted on SharePoint.  

Haha (sad laugh), actually not
really sure what to do here.  One solution is make a Bash script that syncs the relevant files or directories
to the SharePoint version of the project...  One can imagine soft links working ok.  

WAIT!  I KNOW!  

The cookiecutter-data-science template has the `references` directory.  We can agree that that directory
only is synced with SharePoint.  So, yea, create a way of syncing/linking that directory to SharePoint... Would
be nice if a 2-way sync was in place.  

Anyway, just wanted to brainstorm this "out loud"...  If you stumble on this and have 2 cents, please share.



---------------

# References
* [CookieCutter: Read the Docs](https://cookiecutter.readthedocs.io/en/latest/index.html)
* [CookieCutter Data Science Template](https://drivendata.github.io/cookiecutter-data-science/)

