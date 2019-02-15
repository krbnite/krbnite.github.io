---
layout: post
title: Jupyter Jumpstart
tags: python jupyter
---

In Udacity's Deep Learning nanodegree, we will be developing deep learning algorithms 
in Jupyter Notebooks, which promotes literate programming.  These Notebooks are a great way
to develop how-to's and present the flow of one's data science logic.  They are similar to
notebooks in RStudio, but run in the browser and have a "finished feel" by default (whereas,
with R notebooks, you have to compile it to get the finished look).

### Start up a Jupyter session
When starting a new notebook, choose the Python kernel that corresponds to the conda environment you want to 
use.  You can do switch kernels once the notebook is launched.  Since we will be using the Anaconda distribution
of Python, you can also use the conda command line tool to first switch into the correct environment.
```
source activate <envName>
jupyter notebook     # in your project directory
```

Once the new instance is running on the server, you can create a new notebook, text file, directory, or 
terminal.  The Notebook has several tabs.  If the notebook is running in within a conda environment, there will 
be an additional conda tab in your Jupyter session, which allows you to manage your conda environments 
(install/update packages, etc); conda environment can also be managed using the conda command line tool.

Some Pro Tips and Tid Bits:
* before shutting down a Jupyter session, make sure to save your work!!!
* the command palette (keyboard icon) lists all Jupyter commands w/ their keyboard shortcuts  
  - sure, you can try memorizing the keyboard shortcuts, but you can also *just click the command you want to use*
* GitHub will automatically interpret your Jupyter Notebook
* Make a JNB into a blog by downloading MarkDown or HTML file
* Make a JNB into a PDF or a presentation (which will be an HTML file)
* Simplify:  if you just want a regular old Python file,  you can save your Notebook as a regular old python file 

## Juptyter Modality
Jupyter is Modal, similar to Vim: <esc> takes you to Command Mode.

Command Mode Commands:
* Move up and down through cells:  j, k
* Enter a cell for editing:  <enter>    # akin to Vim insert mode
* Exit a cell for control:   <esc>        # akin to Vim normal mode
* Create new cell above, below:    a, b
* Shortcut Help/List:  h
* Convert code cell to MarkDown:  m
* Convert MarkDown cell to code:  y
* Turn on line numbers in a cell:  l
* Delete a cell:  dd
* Save Notebook: s
* Show Command Palette:  <shft>+<cmd>+p  


----------------------------------------------------

### Some Other Useful Stuff
* tab completion (tab)
* the tool tip:  tells you how to use fcn
* basic info (shft+tab)
* more info (shft+tab,shft+tab)


### Magic Keywords
* Inline:  %keyword
* Block:  %%keyword
* Plotting:     %matplotlib [<backEnd>] 
  - e.g.,   %matplotlib inline 
* Code Timing:     timeit
* Interactive Debugging:   pdb
* Learn about how to create your own magics and the list of readily available magics:
http://ipython.readthedocs.io/en/stable/interactive/magics.html

NOTE:  though magics are cool, it's not clear to me whether they are more valuable than simply knowing how 
to do what they do;  primarily this is b/c if you want to convert your IPYNB into a regular ol' python script, it is 
likely that the magics won't be converted properly....


### Command Line Conversions
* HTML:  jupyter nbconvert --to html myNB.ipynb
* LaTeX:  jupyter nbconvert --to latex myNB.ipynb
* PDF: jupyter nbconvert --to pdf myNB.ipynb     #addtn'l flags allow to choose doc type, etc
* MarkDown:  jupyter nbconvert --to markdown myNB.ipynb
* SlideShow( Reveal.js):  jupyter nbconvert --to slides myNB.ipynb
* Executable Script:  jupyter nbconvert --to script myNB.ipynb

Look at the nbconvert documentation...


## What is a Jupter Notebook file anyway (.ipnb)?
It is just a JSON/YAML file like this:
```
 {"cells": [
  {
   "cell_type": "markdown",
   "metadata": {},
   "source": [
    "# Working Through Lunch\n",
    "\n",
    "But a man's gotta eat!\n",
    "\n",
    "Eat and work."
   ]
  },
  {
   "cell_type": "code",
   "execution_count": null,
   "metadata": {
    "collapsed": false
   },
   "outputs": [],
   "source": [
    "# Select the cell, then press Shift + Enter\n",
    "3**2"
   ]
  }
 ],
 "metadata": {
  "anaconda-cloud": {},
  "kernelspec": {
   "display_name": "Python [default]",
   "language": "python",
   "name": "python3"
  },
  "language_info": {
   "codemirror_mode": {
    "name": "ipython",
    "version": 3
   },
   "file_extension": ".py",
   "mimetype": "text/x-python",
   "name": "python",
   "nbconvert_exporter": "python",
   "pygments_lexer": "ipython3",
   "version": "3.5.2"
  }
 },
 "nbformat": 4,
 "nbformat_minor": 0
}
```

---------------------------------------------------------------------------------------------------------------------------

## But Vim... 
Jupyter Notebooks are cool, but.... Sometimes they feel  icky and GUI-ish. In an R notebook, 
you get to code as usual and simply indicate which blocks are MarkDown and which are R code blocks. Then,
when you compile, you see something more like what you would see in a Jupyter Notebook. Wouldn't it be nice to have this feature 
in Python?  Stay in Vim, focus on the code more, generate something pretty when you're done or need a break...?  

These guys basically felt the same way:   
* https://github.com/rossant/ipymd
* https://github.com/lambdalisue/jupyter-vim-binding
* https://www.packtpub.com/books/content/how-connect-your-vim-editor-ipython

I saw that there are some JNB extensions for Vimmishness too.  Will check it out.
