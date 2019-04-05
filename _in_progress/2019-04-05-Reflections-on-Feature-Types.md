
In statistical data analysis, you will often hear about different types of data, specifically whether
a given feature is nominal, ordinal, interval, or ratio.  

I'm here to say that's just the start.  

Often, you will encounter variables that I might call partially-ordinal and ratio-like ordinal.

# Partially Ordinal
By "partially ordinal", I mean when you have values like those found in a family tree: say you a variable called 
`family_member` with levels "Mom", "Dad", "Johnny", and "Sally Sue".  In terms of family lineage, 
* Mom > Johnny
* Mom > Sally Sue
* Dad > Johnny
* Dad > Sally Sue
* But no ordering exists between Johhny and Sally Sue, or Mom and Dad

This can clearly just be considered nominal categorical variable, but nonetheless a partial ordering exists.  Is
there a way to exploit that information better than just dummyizing the variable into 4 binary features?

# Ratio-Like Ordinal 
By ratio-like ordinal, I mean an ordinal variable that has a well-defined zero, which is the distinguishing feature
between ratio and interval variables (in fact, in this parlance, you might consider regular "ordinal" to be 
called "interval ordinal").  

For example, you can have a feature called `number_of_arrests` that is coded like: "none", "one", and "two or more".  With
the raw, granular data, this would be a ratio variable type.  By binning it, it has become ordinal.  But not just
ordinal: it still has a well-defined zero.  This is in contrast to a variable like `temperature` that takes on
the values "super cold", "pretty cold", "meh", "warm", and "hot", which has order, but not necessarily a zero (and
especially not a well-defined zero).  

Again, does the existence of this well-defined zero have intrinsic value that can be served by using one
technique over another?  
