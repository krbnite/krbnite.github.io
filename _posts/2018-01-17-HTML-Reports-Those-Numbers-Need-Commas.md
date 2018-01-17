---
title: HTML Reports&#58; Those Numbers Need Commas!
layout: post
---

In a [previous post](https://krbnite.github.io/pretty-tables-in-pythonic-emails/), 
I showed how to overlay a bar graph in a column of data using the
`styles.bar()` method of a pandas DataFrame, which is a nice feature when coding up
automated emails reports on whatever-metric-your-company-is-interested-in.

<figure>
  <img src="/images/pandas_render_with_bar.png" width="300vw">
</figure>

Now, you'll notice there are no commas on those numbers...and some people find that 
unacceptable.  Admittedly, commas make things easier to more quickly interpret, so
no complaints.  Alright, so the next task would be to add commas... Easy, right?

Not as easy as you might expect! It became a time sink for me, though I did learn
some neat things along the way, such as how to highlight rows in yellow whe the cursor
hovers over 'em. But we will get to that in a separate post.  For now, let's briefly discuss
commas.

## Simple Comma Formatting
In general, formatting a number to have commas is easy, e.g.:
```python
[In] '{:,}'.format(123456789)
[Out]    123,456,789
```

## Pandas Float Format
If you want your DataFrames to default to this when printing to the screen, then you can set an option like so:
```python
pd.options.display.float_format = '{:,}'.format
```

But let's first reset that option and show the before and after of a few things.
```python
[In]  pd.reset_option('display.float_format')
[In]  num = 123456789
[In]  df = pd.DataFrame({'num': [123456789]})
[In]  df
[Out]   
           num
  0  123456789
[In]  num
[Out] 123456789
```

No commas, as expected.  Now lets set the comma option and see what prints out.

```python
[In] pd.options.display.float_format = '{:,}'.format
[In]  df
[Out]   
           num
  0  123456789
[In]  num
[Out] 123456789
```

Again, no commas, as... Wait, what?!  No commas?!  

Well, that is an integer-valued column in the DataFrame, which is a comma-free object  as far as 
pandas is concerned. I wish it weren't so... I wish integer-valued columns automatically got commas as 
well.  But, no. That would be too easy. 

As for the integer variable: pandas doesn't necessarily care that it's an integer, but that it's
not a pandas object.  Even `float(num)` would not benefit from setting pandas options.

```python
[In]  pd.options.display.float_format = '{:,}'.format
[In]
[In]  num = 123456789
[In]  df = pd.DataFrame({'num': [123456789.]})
[In]  df
[Out]   
           num
  0  123,456,789.0
[In]
[In]  num
[Out] 123456789
[In]  float(num)
[Out] 123456789.0
```

## Keeping Those Commas Ain't Easy
Ok, let's remember: the central issue is that we want comma-fied numbers for the bar graph overlay
in our HTML report.  So we convert integer columns to float columns, and proceed to render the 
corresponding HTML...

...only to find that the commas are lost when we use the `styles.render()` method.

```python
df.styles.bar().render()
```

For a second, you might think, "That's ok. I'll just force commas into the numbers by converting the
column to a string:
```python
df.myNumbers = ['{:,}'.format(number) for number in df.myNumbers]
```

Oops! At this point you will find that the `bar()` method will not work.

## Good Enough Solution
We like the bars, but we need commas -- what to do?!

My solution was to notice that the daily value of the metric is most often in the tens
to hundreds of thousands, meaning that I could divide the numbers by 1000 and limit
the need for commas. Just make sure the end user knows that the metric is being reported
in kilo units. 

Side Note: Funnily enough, this only resolved things for like 2 seconds before I was asked,
"Can you put a k next to each number to emphasize the unit?"  That puts us back in the position
of having a string column, which will cause `styles.bar()` to throw an error :-p :-/ :-(

