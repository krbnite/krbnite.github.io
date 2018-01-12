Collection of notes on how to call R from Python, with a focus on how to use 
R's {dplyr} package in Python for munging around.

## Piping
Python is great, but for quick data manipulation and database querying, I long
for {dplyr} from R.  

* [Piping in R and in Pandas](http://fastml.com/piping-in-r-and-in-pandas/)
* [pandas-ply](http://pythonhosted.org/pandas-ply/)



## rpy2
```bash
pip install rpy2
```

http://rpy2.readthedocs.io/en/version_2.8.x/introduction.html#getting-started

```python
from rpy2 import robjects as r
from rpy2.robjects.packages import importr
# r has many r objects, but to be sure you can import default packages
base = importr('base')
stats = importr('stats')
graphics = importr('graphics')

### From Documentation
# Create a Numerical Matrix
#   -- size 100x10 filled with NAs
from rpy2.robjects import NA_Real
from rpy2.rlike.container import TaggedList
m = base.matrix(NA_Real, nrow=100, ncol=10)
# fill the matrix
for row_i in xrange(1, 100+1):
    for col_i in xrange(1, 10+1):
        m.rx[TaggedList((row_i, ), (col_i, ))] = row_i + col_i * 100
```

Most importantly, this seems like one of the most direct ways to use dplyr
in python...  
 - http://rpy2.readthedocs.io/en/version_2.8.x/lib_dplyr.html
 
 From the documentation:
 ```python
from rpy2.robjects.packages import importr, data
datasets = importr('datasets')
mtcars_env = data(datasets).fetch('mtcars')
mtcars = mtcars_env['mtcars']

# D3-style piping ("chaining")
from rpy2.robjects.lib.dplyr import DataFrame
dataf = (DataFrame(mtcars).
         filter('gear>3').
         mutate(powertoweight='hp*36/wt').
         group_by('gear').
         summarize(mean_ptw='mean(powertoweight)'))
         
# Or magrittr/dplyr-style piping
from rpy2.robjects.lib.dplyr import (filter,
                                     mutate,
                                     group_by,
                                     summarize)
dataf = (DataFrame(mtcars) >>
         filter('gear>3') >>
         mutate(powertoweight='hp*36/wt') >>
         group_by('gear') >>
         summarize(mean_ptw='mean(powertoweight)'))
```

My concern is that mixing and matching python/R functionality gets a little
complicated and tedious...

```python
# Define a python function, and make
# it a function R can use through `rternalize`
from rpy2.rinterface import rternalize
@rternalize
def mean_np(x):
    import numpy
    return numpy.mean(x)

# Bind that function to a symbol in R's
# global environment
from rpy2.robjects import globalenv
globalenv['mean_np'] = mean_np

# Write a dplyr chain of operations,
# using our Python function `mean_np`
dataf = (DataFrame(mtcars) >>
         filter('gear>3') >>
         mutate(powertoweight='hp*36/wt') >>
         group_by('gear') >>
         summarize(mean_ptw='mean(powertoweight)',
                   mean_np_ptw='mean_np(powertoweight)'))
```

However... Seems you can bypass this a bit by getting views on the data w/ NumPy.
* http://rpy2.readthedocs.io/en/version_2.8.x/numpy.html

Hmm, you can also use "rmagic" or the rpy2.interactive module directly...

### rpy2.interactive

### rmagic
I'm calling this rmagic, but "rmagic" is now [rpy2.interactive](http://rpy.sourceforge.net/rpy2/doc-2.4/html/interactive.html).
```python
%load_ext rpy2.ipython  # used to be %load_ext rmagic
```
To do some R stuff in a single line:
```python
%R x = c(1,2,3); x^2
```
To do some R stuff in multiple lines:
```python
%%R
x=c(1,2,3)
x^2
# <enter><enter>
```

Numpy2R
```
# lists and pandas dataframes also work as input, but output will be np.array
#  -- actually, not always true; pandas DataFrames can map to R data.frames
a = [1,2,3]
# push python list or numpy array to R
%R -i a a^2
```

R2NumPy
```python
# Return z to python as numpy array
%R -o z q=c(1,2,3); z=q^2
```

In and Out
```python
a = [1,2,3]
%R -i a -o b b = a^2
b
```

### Using dplyr and RPostgreSQL, baby!
Now, sure, you can make fun of me: why not just use python tools?  Fact is,
I do sometimes... I've got some code using sqlalchemy and pandas to connect
to redshift and do queries... For the most part, I love python, but I just keep
missing dplyr and tidyr for my interactive/exploratory work.  I'm excited that
I can now get the most out of python while using what I know and love from R.

```python
%R library(dplyr)  # ignore those pesky warnings
%R libary(RPostgreSQL)
%R con = dbConnect(...)  # i.e., get a connection for yourself
%R a = dbGetQuery("select * from schema.small_test_table")
%%R -o q
q = dbGetQuery(con, "select * from schema.small_test_table") %>%
  select(col1) %>%
  filter(col1 > 3)
  #<enter><enter>
```
F'n beautiful! :-) :-p

## Cool, but you can't really "rmagic" like this in a python script
Ultimatley, in a python script, I have to learn the direct rpy2 way of doing these things.
I'll save that for another day.

### Or can you?! (Next Day)
Uh, actually --- yes, you can, believe it or not.  Obviously the script needs
to be run w/ an ipython interpreter and the right dependencies, but it's possible.


```python
def argh(qstr):
    %R -i qstr -o output output=dbGetQuery(con,qstr)
    return output
argh("select * from busgrp.krbn_test")
```


### Update (2017-07-30)
Note that rpy2.interactive can get complicated using various package managers...  
For example, if you have brew-installed R and Anaconda/Conda, things might work.  However, if
you have to re-install R, things can break...  This is because Conda-managed things do not
necessarily respect HomeBrew-managed things.  If you conda-install rpy2, note that you 
will likely be using a different version of R than the one you normally use on your system.
This means that the R libraries you have previously installed will not be accessible until
you reinstall them in your Conda-R environment...

This last solution is fine.  If you really want to have "one true R", then there is likely
some backflipping you can do w/ path variables, etc.

