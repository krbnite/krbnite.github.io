
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

Again, does the existence of this well-defined zero have intrinsic value that can be served by using one
technique over another?  
