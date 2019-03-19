---
layout: post
title: Cookiecutter Data Science
tags: data-science python easi
---

Way back when circa 2012 I was heavily into R and got introduced to ProjectTemplate, which was
a package that allowed you to start off each new data science project in a similar way -- with a similar
directory structure.  I found it very useful.  Again, when digging a bit into single page apps, I was introduced to this
idea of maintaing a level of homogeneity in the file/folder structure across projects.  For my 
Python-oriented data science/engineering projects at WWE, I kind of came up with my own ideas on how
each project should be structured...which changed over time as I worked on many distinct, but related
projects.  

In my new role, I came into a team that uses `cookiecutter`.  Basically, you can create any kind of
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
remove them or swap them out.  But at the very least, it's a great first draft for developing your own 
template.  You can change things project by project, or modify the template itself and create a new
cookiecutter template (learn more: [read the docs](https://cookiecutter.readthedocs.io/en/latest/index.html)).

