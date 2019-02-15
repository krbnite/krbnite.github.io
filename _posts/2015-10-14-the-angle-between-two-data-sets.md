---
title: The Angle Between Two Data Sets
date: 2015-10-14T22:29:18+00:00
author: Kevin Urban
layout: post
tags: statistics
---
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>
Did you know that a correlation is similar to an inner product between two data sets?

<figure>
<img align="center" src="/images/theta1.png" alt="theta" width="300vw" />
</figure>

&#8220;Huh? Never heard of it.&#8221;

Oh Reader! I&#8217;m sure you&#8217;re kidding. But just in case: an inner product is a measure that allows one to quantify how similar two mathematical objects are. If we&#8217;re talking about two criss-crossing lines in 3D Euclidean space, this amounts to measuring if they are collinear, perpendicular, or somewhere in between. This means that an inner product allows at least a qualitative measure of the &#8220;angle&#8221; between two mathematical objects. Any old inner product can, at the least, tell us whether or not two objects are &#8220;perpendicular&#8221; or not. If one uses a normalized inner product, then one can fully measure the angles between objects.

There might be a lot of terminology here, so an example will help.


A typical inner product in the normal Euclidean setting in 2D or 3D space is the dot product. Say you have two 3D vectors, $$A = (A_{1},A_{2},A_{3})$$ and $$B=(B_{1},B_{2},B_{3})$$. (The results below are applicable to 2D vectors by simply assuming that $$A_{3}=0$$ and $$B_{3}=0$$.) The dot product of these two vectors can be written in two important ways, both of which will give you the same answer. Deciding on which identity you use simply depends on your preference, or possibly how you collected your data: did you measure rectilinear components, magnitudes and angles, or both?<!--more-->


<figure>
<img src="/images/inner-product1.png" alt="inner-product" width="600vw" />
</figure>


* Inner Product (Def1): $$ \quad Dot(A,B) = \Sigma_{i=1}^{3}A_{i}B_{i} = A_{1}B_{1}+A_{2}B_{2}+A_{3}B_{3} $$
* Inner Product (Def2): $$ \quad\quad Dot(A,B) = \|A\|\|B\|cos(\theta) $$

Here we use the notation $$\|A\|$$ to denote the norm (aka the length) of the vector A. The norm is defined as follows:
* IP-Induced Norm: $$\quad\quad Norm(A) = \sqrt{Dot(A,A)} = \sqrt{\|A\|^{2}cos(0)} = \|A\| $$

An important observation here is that the inner product (qualitative mixed measurement of angles and lengths) on the space naturally induces a definition of the norm (length) on that space.

Furthermore, the equality in Eq(2) tells us that if we can define a normalized inner product rather easily:

* Normalized Inner Product: $$\quad\quad NormDot(A,B) = \frac{Dot(A,B)}{\|A\|\|B\|} = cos(\theta)$$

Having this normalized inner product, we then see that we can quantitatively compute the angle between any two vectors in this space:

* Angle: $$\quad\quad \theta =acos\left(\frac{A\cdot B}{\|A\|\|B\|}\right) $$

The important take-away here is that in these simple vector spaces, the notions of vector components, vector magnitudes, and angles between vectors are all intuitive. In fact, mathematically speaking, the generalized definitions of inner product and norm was motivated by the desire to generalize these geometric notions to other mathematical spaces.

The ideas generalize to N-dimensional Euclidean spaces without any required thinking: all of the definitions we worked out above are immediately applicable to N-dimensional vectors. That is, although we might not be able to picture it in our mind, in a real-valued N-dimensional space, we mathematically have the quantified notions of vector magnitude, vector components, and the angle between two vectors. If we&#8217;re interested in variables that are composed of complex numbers, these definitions even generalize to complex vector spaces with very little thought (for example, the N-dimensional Hilbert spaces in quantum mechanics). Most things we measure on a day-to-day basis, however, have real-number values (stock prices, wind speed,  heights, weights, etc), so for the moment, let&#8217;s not make the situation any more complex.

&#8220;Pun intended?&#8221;

Damn right, Reader. Pun intended!

### From N-Dimensional Euclidean Space to N-Element Data Sets
  
We&#8217;ll get from Euclidean Space to Data Space by imagining that we&#8217;ve collected two associated sets of mean-subtracted numerical data, $$A = (A_{1},A_{2},...,A_{N})$$ and $$B = (B_{1},B_{2},...,B_{N})$$. The &#8220;association&#8221; aspect is important because correlation can only be used to measure a relationship between two stochastic/random variables whose instances can be meaningfully paired together: for two time series, the association might be the instants of time each data stream were simultaneously measured at; if it a data set about people (e.g., height, weight, income), then the association tying the two data sets together are the individuals. If you cannot meaningfully pair two data sets, then you cannot compute a correlation, e.g., if you have an N-element data set of rock sizes and another N-elements data set of people&#8217;s heights, how would you meaningfully pair these data sets? The answer is that you can&#8217;t&#8212;not unless you&#8217;re doing a study that specifically pairs these variables, e.g., if you&#8217;ve collected data concerning what-sized rock different individuals first pick up when they are placed in a rock quarry for an hour.

### The Geometry of Statistics
  
In the statistical setting, the sample covariance for a mean-subtracted data set is written:
  
* $$\quad\quad Cov(A,B) = \frac{1}{N-1}\Sigma_{i}^{N}Α_{i}B_{i}$$

The sample standard deviation is just the square root of the sample variance&#8212;and variance is just a special case of covariance:
  
* $$ std(A) = \sqrt{Var(A)} = \sqrt{Cov(A,A)} = \frac{1}{N-1}\Sigma_{i=1}^{N}A_{i}^{2} $$

The sampled correlation coefficient has the following definition:
  
* $$ \rho(A,B) = \frac{Cov(A,B)}{\sqrt{Var(A)Var(B)}} $$

Note that you can explicitly compute an &#8220;angle between data sets&#8221; by allowing the above equation to generalize to this situation:
  
* $$ Cov(A,B) = std(A)StdDev(B)cos(\theta)$$
  
* $$ \theta = acos\left(\frac{Cov(A,B)}{std(A)std(B)}\right)$$

Given this generalization to the statistical setting, you can see that the correlation coefficient is not just similar to a cosine, but is literally a cosine:

<center>
  $$\rho(A,B) = \frac{Cov(A,B)}{std(A)std(B)} = cos(\theta)$$
</center>The above equations are marked as asterisked versions of the previous equations for a pretty obvious reason. C

<span style="line-height: 1.7em;">ovariance looks suggestive of an inner product, doesn&#8217;t it? An inner product on a real-valued vector space is characterized by <strong>linearity</strong> (IP(aX+Z,Y)=aIP(X,Y)+IP(Z,Y), <strong>symmetry in its arguments</strong> (IP(A,B)=IP(B,A)), and <strong>positive-definiteness</strong> (IP(X,Y) \(\ge\) 0, the equality of which is reached only for X=Y=0). </span>

Correlation is certainly symmetric in its arguments and positive definite. Without further thought, it even satisfies the scalar portion of the linearity property (IP(aX,Y)=aIP(X,Y)). If we can conjure up a useful or believable definition of vector addition ([which seems possible](http://www.pas.rochester.edu/~douglass/papers/Towsley_Pak_Douglass_published_.pdf)), then correlation <span style="line-height: 1.7em;">is an inner product! By analogy to Euclidean space, then, a covariance is some mixed measure of magnitude and angle between two mean-subtracted data sets. Just like the</span><span style="line-height: 1.7em;"> Euclidean </span><span style="line-height: 1.7em;">case</span><span style="line-height: 1.7em;">, this particular inner product induces a norm&#8212;the standard deviation&#8212;which is somehow, by analogy, a measure of the data vector&#8217;s magnitude. </span><span style="line-height: 1.7em;">W</span><span style="line-height: 1.7em;">e can also now see that the correlation coefficient is a normalized inner product:&#8212;a measure of the angle between two data vectors.</span>

I think maybe the craziest thing here is that the statistical concepts we&#8217;re so familiar with in the data setting are not just analogous to geometrical notions from Euclidean space, but literally are the geometrical notions fudged by a scalar&#8212;at least for mean-subtracted data sets.

### Implications
  
Correlations coefficients are not additive quantities, and the geometric notions presented here should give you an idea of why. Cosines, in general, are not additive: cos(θ) + cos(φ) does not equal cos(θ+φ). However, one can find examples on the internet of people improperly computing arithmetic averages of correlation coefficients, for example, in an attempt at a meta-analysis of published results in some research field. Perhaps they think they are measuring the central tendency of the correlation coefficients, convinced that they have uncovered the true population parameter. Unfortunately, in cases like this, the arithmetic average does not necessarily represent an obvious or meaningful measure&#8212;it is not a robust measure of central tendency.

<span style="text-decoration: underline;"><strong style="line-height: 1.7em;">SUMMARY TABLE</strong></span>

<table border="1">
  <tr>
    <td>
    </td>
    
    <td>
      <strong>N-Dimensional Euclidean Vectors</strong>
    </td>
    
    <td>
      <strong>N-Element Mean-Subtracted Data Sets</strong>
    </td>
    
    <td>
      <strong>Relationship</strong>
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>Inner Product (Def1)</strong>
    </td>
    
    <td>
      \(Dot(A,B) = \Sigma_{i=1}^{N}A_{i}B_{i}\)
    </td>
    
    <td>
      \(Cov(A,B) = \frac{1}{N-1}\Sigma_{i}^{N}Α_{i}B_{i}\)
    </td>
    
    <td>
      \(Cov(A,B) = \frac{1}{N-1}Dot(A,B)\)
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>Inner Product (Def2)</strong>
    </td>
    
    <td>
      \(Dot(A,B) = |A||B|cos(\theta)\)
    </td>
    
    <td>
      \(Cov(A,B) = std(A)std(B)cos(\theta)\)
    </td>
    
    <td>
      \(Cov(A,B) = \frac{1}{N-1}Dot(A,B)\)
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>Norm</strong>
    </td>
    
    <td>
      \(|A| = \sqrt{Dot(A,A)} \)
    </td>
    
    <td>
      \(std(A) = \sqrt{Cov(A,A)}\)
    </td>
    
    <td>
      \(std(A) = \sqrt{\frac{1}{N-1}}|A|\)
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>Normalized Inner Product (Def1)</strong>
    </td>
    
    <td>
      \(NormDot(A,B) = \frac{Dot(A,B)}{|A||B|}\)
    </td>
    
    <td>
      \(\rho(A,B) = \frac{Cov(A,B)}{std(A)std(B)}\)
    </td>
    
    <td>
      \(\rho(A,B) = NormDot(A,B) \)
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>Normalized Inner Product (Def2)</strong>
    </td>
    
    <td>
      \(NormDot(A,B) = cos(\theta)\)
    </td>
    
    <td>
      \(\rho(A,B) = cos(\theta)\)
    </td>
    
    <td>
      \(\rho(A,B) = NormDot(A,B) \)
    </td>
  </tr>
  
  <tr>
    <td>
      <strong>Angle</strong>
    </td>
    
    <td>
      \(\theta = acos\left(\frac{A\cdot B}{|A||B|}\right)\)
    </td>
    
    <td>
      \(\theta = acos\left(\frac{Cov(A,B)}{std(A)std(B)}\right)\)
    </td>
    
    <td>
      \(\theta=\theta\)
    </td>
  </tr>
</table>

* * *

More Reading:
  
[Cross-Correlation on Wikipedia](https://en.wikipedia.org/wiki/Cross-correlation)

NOTE: this article is a beefed-up version of an answer I provided on StackExchange:  <a class="question-hyperlink" style="font-size: 16px; font-weight: 600; line-height: 1.7em;" href="http://stats.stackexchange.com/questions/134774/non-additive-property-of-correlation-coefficients">Non-additive property of correlation coefficients</a>

