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

---------------

# Mix in a Few More Reality Bits
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
  - If you haven't used cookiecutter-data-science yet:  `cookiecutter https://github.com/drivendata/cookiecutter-data-science`
  - For subsequent projects: `cookiecutter cookiecutter-data-science`
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

Anyway, just wanted to brainstorm this in the form of a blog.  If you stumble on this and have 2 cents, please share.



---------------

# References
* [CookieCutter: Read the Docs](https://cookiecutter.readthedocs.io/en/latest/index.html)
* [CookieCutter Data Science Template](https://drivendata.github.io/cookiecutter-data-science/)

