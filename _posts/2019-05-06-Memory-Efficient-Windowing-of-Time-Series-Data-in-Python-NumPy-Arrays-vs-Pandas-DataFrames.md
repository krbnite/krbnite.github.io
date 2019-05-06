---
title: Memory-Efficient Windowing of Time Series Data in Python&#58; 2. NumPy Arrays vs Pandas DataFrames
layout: post
tags: time-series pandas numpy python 
---

In the [previous post](https://krbnite.github.io/Memory-Efficient-Windowing-of-Time-Series-Data-in-Python-Memory-Strides-in-NumPy/), we ignored the existence of Pandas and did things in pure NumPy.  There was a
really important reason for this:  Pandas DataFrames are not stored in memory the same as default NumPy
arrays.

This is nontrivial: reading and learning about NumPy's `as_strided` function is often in the context
of a default NumPy array.  I emphasize "default" here so that it is obvious there is more to NumPy than
meets the default eye.

As mentioned in the previous post, the default NumPy array is a C-like array, which means that it is
stored in memory in "row major" fashion...which means that, give a 64-bit float array with 10 columns:
1. to get from one element in a row to the next (i.e., to get from one column to the next in the same 
   row), you must stride your "data eye" 8 bytes forward, and
2. to go from one element in a column to the next (i.e., to get from one row to the next within the same
   column), you must stride your "data eye" across 80 bytes (that's the k remaining 8-byte elements 
   in the current row, and the initial "10-k" 8-byte elements in the next row).

"Ok, Kevin -- that all sounds...reasonably complex.  But what's the big deal?  Pandas DataFrames are built
atop Numpy arrays.  So if you have a Pandas DataFrame, `df`, can't you just work with its underlying NumPy
array, `arr = df.values`?"

Great question, Reader!  But the answer is "NO!" and I'll explain why.  But first, let's just see the
horror and mysterious behavior in action.


# Single List: Something's Fishy
```python
import numpy as np
import pandas as pd

# Create lists that we will use for arrays and DataFrames
a = [0,1,2,3,4,5,6,7,8,9]
b = [1,1,1,1,1,1,1,1,1,1]
c = [0,0,0,0,0,0,0,0,0,0]
```

First things first: let's create a simple 1D array and 1D DataFrame.

```
npa = np.array(a)
dfa = pd.DataFrame({'a': a})

#-------------------------------------------------------------
# What do they look like?
#-------------------------------------------------------------
npa
  array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])

dfa
     a
  0  0
  1  1
  2  2
  3  3
  4  4
  5  5
  6  6
  7  7
  8  8
  9  9

#-------------------------------------------------------------
# How are they shaped?
#-------------------------------------------------------------
npa.shape
  (10,)
dfa.shape  # same as dfa.values.shape
  (10,1)
  
#-------------------------------------------------------------
# How are they strided (in memory)?
#-------------------------------------------------------------
npa.strides
  (8,)
dfa.values.strides
  (8,80)
```

So far, these are not huge differences.  We see that the DataFrame automatically assumes a 2D array,
even if only given one column of data.  We can get these things to agree better with a little reshaping (but
this is a poor bandaid, as you will see shortly).

```python
# Make NumPy array more like the Pandas DataFrame
npa = npa.reshape(10,1)
npa.shape
  (10,1)
npa.strides
  (8,8)
npa
  array([[0],
        [1],
        [2],
        [3],
        [4],
        [5],
        [6],
        [7],
        [8],
        [9]])
```

Ok, well now we have the DataFrame shape...but what's going on with those strides?  For the NumPy
array, the data structure says that one need only stride 8 Bytes along the row or column to 
get to the next element (`strides = (8,8)`).  This makes sense: since a NumPy array stores its elements contiguously,
going to the next element in a 1-element row is the same as going to the next element in the column.  However,
the data structure underlying the DataFrame tells us the strides are `(8,80)`.  From what we understand about
strides and NumPy arrays, this seems to tell us the following:  one must stride over
8 Bytes to get to the next element in a row, but stride 80 Bytes (10 elements) to get to the next element
of a column...  

That seems fishy...  What gives?  

We'll find out a little more when we go to look at multiple columns.



# Multiple Lists to Multiple Columns: The Fish Rears its Ugly Head
Ok, so now let's use the lists a, b, and c that we create above to create some 3-column NumPy arrays
and explore how deep this black rabbit worm hole goes!  

Above, we saw that we needed to specially shape the NumPy array corresponding to list `a` so 
that the shape and look of the NumPy array matched the DataFrame.  You will find that this same
reshaping is one way to properly "paste" (concatenate) the lists together into columns in the NumPy 
array.  I'll show two ways below, which result in very similar arrays...and yet very different
arrays, especially as far as intelligently using something like NumPy's `as_strided` function.

```
# NP Array 1:  Transpose the list of lists
np1 = np.array([a,b,c]).T
np1
  array([[0, 1, 0],
        [1, 1, 0],
        [2, 1, 0],
        [3, 1, 0],
        [4, 1, 0],
        [5, 1, 0],
        [6, 1, 0],
        [7, 1, 0],
        [8, 1, 0],
        [9, 1, 0]])

# NP Array 2: Concatenate column vectors
vec_a = np.array(a).reshape(10,1)
vec_b = np.array(b).reshape(10,1)
vec_c = np.array(c).reshape(10,1)
np2 = np.concatenate([vec_a, vec_b, vec_c], axis=1)
np2
  array([[0, 1, 0],
        [1, 1, 0],
        [2, 1, 0],
        [3, 1, 0],
        [4, 1, 0],
        [5, 1, 0],
        [6, 1, 0],
        [7, 1, 0],
        [8, 1, 0],
        [9, 1, 0]])
```

Well, they sure look the same!  Let's see if they are "equal":  

```python
# Shape
np1.shape
  (10,3)
np2.shape
  (10,3)

# Values
np1 == np2
  array([[ True,  True,  True],
        [ True,  True,  True],
        [ True,  True,  True],
        [ True,  True,  True],
        [ True,  True,  True],
        [ True,  True,  True],
        [ True,  True,  True],
        [ True,  True,  True],
        [ True,  True,  True],
        [ True,  True,  True]])
```

So, they look the same, they're shaped the same, and apparently they are the same!

Except that they are **NOT** the same... Not as far as how they are stored, which you
can gain insight into by looking at their memory strides:

```python
np1.strides
  (8,80)
  
np2.strides
  (24,8)
```

If you're scratching your head, you're not alone.  Someone else out there is definitely scratching
their head!  

What does this all mean?!

We initially learned that the default NumPy array is C-like, or "row major," which seems to fit the
strides of `np2`: 
* move 8 Bytes to get to the next row element (e.g., starting from the intial element in row 1,
move 8 Bytes to get to the next column in that row)
* move 24 Bytes get to the next column element (e.g., to get from the initial element 
in row 1 to the intial element in row 2, move 24 Bytes -- passing all elements in row 1). 

But this logic does not neatly fit into the strides we found for `np1`.  At first glance, it seems nonsensical 
to "move 8 Bytes to get to the next row" and to "move 80 Bytes to get to the next column"... That is, unless
we consider the columns "rows" and the rows "columns" -- in other words, it does make sense if we imagine
this array is laid out in memory column-by-column instead of row-by-row.

This is, in fact, exactly what is happening: `np1` is Fortran-like, i.e., written in memory column-by-column,
which is referred to as "column major" array storage.  

Let's first show this by displaying the metadata of each array, then we'll explain a little bit and
move on to the Pandas DataFrame.

The appropriate metadata is stored in the `flags` attribute of a NumPy array:

```python
# np1 is a Fortran-like array (column major memory storage)
np1.flags
  C_CONTIGUOUS : False
  F_CONTIGUOUS : True
  OWNDATA : False
  WRITEABLE : True
  ALIGNED : True
  WRITEBACKIFCOPY : False
  UPDATEIFCOPY : False

# np2 is a C-like array (row major memory storage)
  C_CONTIGUOUS : True
  F_CONTIGUOUS : False
  OWNDATA : True
  WRITEABLE : True
  ALIGNED : True
  WRITEBACKIFCOPY : False
  UPDATEIFCOPY : False
```

Not all arrays are either C-like or F-like: 1D arrays are both, by definition, since there
is only 1 stride parameter that means anything -- how many Bytes to move to next element in
sequence.  For an array shape like (n,), whether you consider the sequence a row or a column \
in your head is just a mental picture.  Sometimes we will paint our mental picture onto a 1D 
array (e.g., `np.array([1,2,3]).reshape(3,1)`), but this does not affect the memory storage of the 
array itself (though it may be an important consideration for how that array can be operated on). 

Here is some show-and-tell for 1D NumPy arrays:

```python
np.array(a).flags
  C_CONTIGUOUS : True
  F_CONTIGUOUS : True
  OWNDATA : True
  WRITEABLE : True
  ALIGNED : True
  WRITEBACKIFCOPY : False
  UPDATEIFCOPY : False

np.array(a).T.flags
  C_CONTIGUOUS : True
  F_CONTIGUOUS : True
  OWNDATA : False
  WRITEABLE : True
  ALIGNED : True
  WRITEBACKIFCOPY : False
  UPDATEIFCOPY : False

np.array(a).reshape(10,1).flags
  C_CONTIGUOUS : True
  F_CONTIGUOUS : True
  OWNDATA : False
  WRITEABLE : True
  ALIGNED : True
  WRITEBACKIFCOPY : False
  UPDATEIFCOPY : False
```

Continuing with this, we can that the parity breaks down when we 
reshape the list into something 2D:

```python
np.array(a).reshape(5,2).flags
  C_CONTIGUOUS : True
  F_CONTIGUOUS : False
  OWNDATA : False
  WRITEABLE : True
  ALIGNED : True
  WRITEBACKIFCOPY : False
  UPDATEIFCOPY : False
```

The reshaped list defaults to C-like memory storage, which we already knew.  However,
if we reshape, then take the transpose, the array will be F-like (column major memory
storage).

```python
np.array(a).reshape(5,2).T.flags
  C_CONTIGUOUS : False
  F_CONTIGUOUS : True
  OWNDATA : False
  WRITEABLE : True
  ALIGNED : True
  WRITEBACKIFCOPY : False
  UPDATEIFCOPY : False
```


This explains why the array, `np1`, we created above appear to have column major memory
storage -- because it did, because we used the transpose operation in defining it!



# Pulling the Pandas DataFrame out of the Fish Bucket
This is all important for understanding NumPy arrays in general, and the things that
happen underneath the hood.  But it's also very important for understanding a major 
difference between default NumPy arrays and Pandas DataFrames:
* default NumPy arrays are C-like (row major memory structure)
* Pandas DataFrames sometimes default to F-like (column major memory structure), depending on
  how you initialize them

This might have been clear above, when we saw that the strides of `np1` were `(8,80)`, which is
the same stride tuple that we saw for the 1-column DataFrame.  In fact, this will be the 
stride tuple for the 3-column DataFrame too!

```python
df0 = pd.DataFrame({'a': a, 'b': b, 'c': c})
df0.values.strides
  (8,80)
```

The astute reader may have noticed I called this `df0`, implying there will likely be
a `df1`.  The super duper, mega astute master of reading and comprehension will also have
noticed that the NumPy examples started their count at "1", `np1` and `np2`, where as this Pandas
example began at "0", indicating
even more mayhem to come!

The MayheM:  Let's not forget that there are many ways to create a Pandas DataFrame.  The dictionary method above is
one.  Explicitly wrapping over a NumPy array is another.


```python
df1 = pd.DataFrame(np1)
df1.values.strides
  (8,80)
  
df2 = pd.DataFrame(np2)
df2.values.strides
  (24,8)
```

So, it would appear that creating the DataFrame using the dictionary method creates
its underlying NumPy array similarly to how we created `np1` (for reference:
`np1 = np.array([a,b,c]).T`).  However, we also see that a DataFrame created from
a pre-existing NumPy array keeps the stride structure of that NumPy array.

Let's look at the flags to dig deeper:

```python
df0.values.flags
  C_CONTIGUOUS : False
  F_CONTIGUOUS : True
  OWNDATA : False
  WRITEABLE : True
  ALIGNED : True
  WRITEBACKIFCOPY : False
  UPDATEIFCOPY : False

df1.values.flags
  C_CONTIGUOUS : False
  F_CONTIGUOUS : True
  OWNDATA : False
  WRITEABLE : True
  ALIGNED : True
  WRITEBACKIFCOPY : False
  UPDATEIFCOPY : False

df2.values.flags
  C_CONTIGUOUS : True
  F_CONTIGUOUS : False
  OWNDATA : False
  WRITEABLE : True
  ALIGNED : True
  WRITEBACKIFCOPY : False
  UPDATEIFCOPY : False
```

Well, digging deeper did not necessarily unearth anything new here, but we do confirm
that both df0 and df1 have F-like (column major) array storage, while df2 has C-like (row
major) array storage.

One last, important consideration is how CSV files come into play.

In order to introduce no accidental structure, create the CSV file outside of Python using
Vim (or some inferior editor ;-p), like so

```vim
a,b,c
0,1,0
1,1,0
2,1,0
3,1,0
4,1,0
5,1,0
6,1,0
7,1,0
8,1,0
9,1,0
```

Now save that file as `test.csv` and read it into Pandas DataFrame!

```python
df3 = pd.read_csv('test.csv')

df3.values.strides
  (8,80)

df3.values.flags
  C_CONTIGUOUS : False
  F_CONTIGUOUS : True
  OWNDATA : False
  WRITEABLE : True
  ALIGNED : True
  WRITEBACKIFCOPY : False
  UPDATEIFCOPY : False
```

There you have it, folks!  Reading a CSV file defaults to the same F-like behavior we
saw when creating the DataFrame via a dictionary.  

I cannot emphasize this enough: **in general, the default Pandas DataFrame is F-like, while the
default NumPy array is C-like**.  This can impact your data analysis if your algorithm assumes
C-like arrays and your user (e.g., YOU!) is unknowingly using F-like arrays.

# Using NumPy's `as_strided` Function with DataFrames
The post references "memory efficiency" and yet we spent most of the post poring over the
details of how various data structures are stored in memory.  Well, that was all for a really
good point:  in the [previous post](https://krbnite.github.io/Memory-Efficient-Windowing-of-Time-Series-Data-in-Python-Memory-Strides-in-NumPy/), we developed a lot of great machinery that works splendid
with default, C-like NumPy arrays, but gives nonsensical results for default, F-like Pandas
DataFrames.

To show this, let's just do a few experiments.

```python
# Create lists 
a = [0,1,2,3,4,5,6,7,8,9]
b = [1,1,1,1,1,1,1,1,1,1]
c = [0,0,0,0,0,0,0,0,0,0]

# Create column vectors  &  C-like NumPy array
vec_a = np.array(a).reshape(10,1)
vec_b = np.array(b).reshape(10,1)
vec_c = np.array(c).reshape(10,1)
arr = np.concatenate([vec_a,vec_b,vec_c], axis=1)

# Create F-like Pandas DataFrame
df = pd.DataFrame({'a': a, 'b': b, 'c': c})

# Show for yourself that arr and df have same shape and look
pass

# Run arr and df through the make_views function (created in last post)
win_size = 2
step_size = 1
arr_views = make_views(arr, win_size, step_size)
df_views = make_views(df.values, win_size, step_size)

# For arr_views, show that things work out like we planned in the last post
arr_views[0:3]
   array([[[0, 1, 0],
         [1, 1, 0]],

         [[1, 1, 0],
         [2, 1, 0]],

         [[2, 1, 0],
         [3, 1, 0]]])
 
# For df_views, show that things did not work out like we planned 
df_views[0:3]
   array([[[0, 1, 2],
         [3, 4, 5]],

         [[3, 4, 5],
         [6, 7, 8]],

         [[6, 7, 8],
         [9, 1, 1]]])
```

This is monumental.

I didn't even get an error message.

If I was to have kept this in the processing pipeline, I might have gotten horrible, confusing, or
misleading results...and I might not have known why.

That said, I immediately identified the discrepancy, thus these posts :-p

Note that this issue is not only important for using Pandas DataFrames, but is even an issue for
transposed NumPy arrays, etc. 

In my next post on this topic, I will create a function that respects the C-like or F-like nature
of the incoming array.


# One last, off-topic observation
We have spent most of this post focusing on "how" a NumPy array or Pandas DataFrame is stored 
in memory, but in the spirit of "memory efficient" time series you might also wonder whether
the DataFrame has a larger memory footprint than the default NumPy array.

Without going too deep, I'd just say, "Not really!"  

Below, I leave you off with an example that shows a Pandas DataFrame having the same size as
a similar NumPy array we created in the [previous post](https://krbnite.github.io/Memory-Efficient-Windowing-of-Time-Series-Data-in-Python-Memory-Strides-in-NumPy/).


```python
n = 86400 * 30   # 30 days of 1-Hz data

df = pd.DataFrame({
  'mag1_x': np.random.randn(n), 'mag1_y': np.zeros(n), 'mag1_z': np.ones(n),
  'mag2_x': np.random.randn(n), 'mag2_y': np.random.gamma(1,1,n),  'mag2_z': np.random.randn(n),
  'mag3_x': np.random.randn(n), 'mag3_y': np.zeros(n), 'mag3_z': np.ones(n),
  'mag4_x': np.random.randn(n), 'mag4_y': np.random.gamma(1,1,n),  'mag4_z': np.random.randn(n),

})

# Memory Estimate (Pandas Style)
f'{round(df.memory_usage().sum()/1.0e6,2)} MB'
  248.83 MB
  
# Memory Estimate (Again, but NumPy Style)
f'{round(df.values.nbytes/1.0e6,2)} MB'
  248.83 MB
```
