---
title: 'Better Jupyter Notebook'
date: 2017-04-20
author: Kevin Urban
layout: post
categories:
  - python
  - jupyter notebook
  - Uncategorized
---

At one point, long ago, I was using MatLab for this project, IDL for that project, and R for yet another.  For each language, I 
used a separate IDE, and this introduced a productivity bottleneck.  

Keyboard commands changed between IDEs, as well layout and other little things that "don't matter," but really kind of do.
My solution was to check out this thing called "Vim" that I kept hearing about, but alas! Every article that sings Vim's high praises also 
references its learning curve.  This can make Vim appear uninviting, especially if your to-do list is already overwhelming.  Fortunately,
when I started out (circa wintertime 2013-2014), there was [Vim Adventures](https://vim-adventures.com/).  This game got me up and running
some Vim basics, and I can't thank its creator enough.

At one point I realized that I don't need Vim so much as I need an IDE to support Vim bindings.  For example, I do a lot of 
work in R, and almost exclusively use RStudio to do it. The upside is everything that RStudio has to offer in addition to supporting Vim basics.
The downside is that it doesn't yet support customizations. (Please, correct me if I'm wrong!) All things considered, RStudio's Vim support is 
good enough and will likely only get better over time.

This experience, however, has gotten me used to a certain type of lifestyle!  A lifestyle that has made me soft.  But, unlike 
years ago when I was all about doing things the hard way, I'm not embarrassed of being soft.  In fact, I want to be softer!  
The other half of my projects these days are in Python, and I find myself wishing I could just do them in RStudio.  

At first I tried a "vanilla" Jupyter Notebook, but didn't like the absence of a more interactive Python command line.  Worse, no native 
support.  So I tried Spyder...it is RStudio-like, but no Vim support.  Then there's Rodeo, which is almost good enough (and probably
will be given some time).   

None of these options fully satisfied me.  Jupyter Notebooks won out because other than my aforementioned gripes, these things are
beautiful for putting together well-documented, well-motivated data science projects from beginning to end -- from Notebook to HTML
page, to presentation slides, and so on!

## Jupyter Notebooks FTW!
Since opting for the good ol' vanilla JNB, I learned more about Jupyter magics and extensions.  Turns out, some pretty smart people 
already had some of my gripes --- and did something about it!  

[Vim-like editing](https://github.com/lambdalisue/jupyter-vim-binding)?  Check.  

An extra pane for a more interactive Python command line?  

"Do you mean, like, some kind of [Scratchpad](https://github.com/minrk/nbextension-scratchpad)?" (Check.)

## Installing Notebook Extensions
http://jupyter-contrib-nbextensions.readthedocs.io/en/latest/
http://jupyter-contrib-nbextensions.readthedocs.io/en/latest/install.html
conda install -c conda-forge jupyter_contrib_nbextensions

After you do this, open a new jupyter notebook session.  On the main page, as one of the tabs, you should see Nbextensions

The ones I've played with so far include:

* Scratchpad:  Good ol' Python command prompt for quick, exploratory work.
* Table_beautifier:  Vanilla JNB has fairly ugly, fairly useless tables.  As wonderful as a JNB is for a data science project and 
its documentation, it’s a hard sell to those used to Excel.  And why not?  Being able to re-sort a table by any of its columns is basic.  
Thankfully, table_beautifier adds this to JNBs.  (That said, if you’ve organized a table in some special way, and you click a re-sort, 
then you can funk your table up and not get it back to normal... Still playing around with it though.)
* ExecuteTime:  Why not?  Ever run a code and wish you’d timed it?  Wish no more.  You can tweak it to give the info you want, how you want...which I haven’t done, but should.
* AddBefore:  Simple, but wonderful.  Adds a button to place a new cell above the current active cell.
* Drag and Drop:  Pretty cool!
* LaTeX environments for Jupyter:  'nuff said.
* Table of Contents (2):  I really like this.  Gives you the ability to quickly jump to different sections of your notebook.


## Magics
SQL magic and so on.  Good stuff!




