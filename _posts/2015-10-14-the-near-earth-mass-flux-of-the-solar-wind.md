---
id: 353
title: 'The Near-Earth Mass Flux of the Solar Wind:  Constant, Capricious, or Both?'
date: 2015-10-14T15:50:46+00:00
author: ogkubl
layout: post
guid: http://kevin-urban.com/blog/?p=353
permalink: /index.php/2015/10/14/the-near-earth-mass-flux-of-the-solar-wind/
categories:
  - hydromagnetic
  - solar wind
  - ULF
  - Uncategorized
---
[<img class="wp-image-464 alignright" src="http://kevin-urban.com/blog/wp-content/uploads/2015/10/solar-wind-mass-flux-300x109.png" alt="solar-wind-mass-flux" width="308" height="112" srcset="http://kevin-urban.com/blog/wp-content/uploads/2015/10/solar-wind-mass-flux-300x109.png 300w, http://kevin-urban.com/blog/wp-content/uploads/2015/10/solar-wind-mass-flux-1024x372.png 1024w" sizes="(max-width: 308px) 100vw, 308px" />](http://kevin-urban.com/blog/wp-content/uploads/2015/10/solar-wind-mass-flux.png)

In a [meta-analysis](http://kevin-urban.com/blog/index.php/2015/09/30/early-characterizations-of-the-mid-latitude-dayside-hydromagnetic-response-to-upstream-solar-wind-conditions-1981-82/#polynomial) of early (1980-82) papers by Allan Wolfe (and Co) on the upstream solar wind control of ground-level hydromagnetic [GLHM] activity (a.k.a. ULF waves), I conjectured that the data analysis in <a style="line-height: 1.7em;" href="http://onlinelibrary.wiley.com/doi/10.1029/JA085iA11p05977/abstract">Wolfe1980b</a><span style="line-height: 1.7em;"> indicates that the mass flux of the solar wind might be constant. Wolfe showed that there was a strong relationship between the upstream solar wind speed and density, the scatterplot illustrating that the upstream density to decay nonlinearly as a function of the speed. It occurred to me that the scatterplot of \(V_{SW}\) vs \(N_{p}\) looked fairly hyperbolic, as if \(N_{p}=\frac{A}{V_{SW}}\)</span>

This conjecture had implications for Wolfe&#8217;s conclusions and analyses in his other papers:

  1. Given the solar wind speed is considered a primary driver of GLHM energy in the context of correlation (a measure of linearly co-varying observables), then it should be expected that the density would appear to be an unimportant upstream parameter since its covariation with GLHM energy would be hyperbolic.
  2. If the mass flux (\( \mathscr{M}=m\_{p}N\_{p}V\_{SW}\)) is constant, this means that the momentum flux (\(\mathscr{P}=m\_{p}N\_{p}V\_{SW}^{2}\)) is really a measure of \(V\_{SW}\) (i.e., \(\mathscr{P}=BV\_{SW}\)) and the kinetic energy flux density (\(f\_{k}=\frac{1}{2}m\_{p}N\_{p}V\_{SW}^{3}\)) is really a measure of \(V\_{SW}^{2}\) (\(f\_{k}=CV_{SW}^{2}\)), indicating that a polynomial regression on solar wind speed alone might suffice for Wolfe&#8217;s purposes:

<p style="text-align: center;">
  log(GLHM Energy) = \(A+BV+CV^{2}+DV^{3}\)
</p>

As I thought about the constancy of the mass flux in the solar wind since these posts, I realized that I hadn&#8217;t come up with this idea independently&#8212;even if, at the moment, it had felt like that. I had heard about it before, while attending the Heliophysics Summer School, <a style="line-height: 1.7em;" href="https://www.vsp.ucar.edu/Heliophysics/pdf/SolarWind-Kasper-2.pdf" target="_blank">in a session led by Justin Kasper</a> (pdf). In the magnetospheric community, the solar wind is an input: in a sense, we can take it empirically at face value. But in the solar physics community, the solar wind is an output&#8212;and so understanding why the solar wind&#8217;s mass flux at the distance of Earth&#8217;s orbit is approximately constant is actually a huge issue (e.g., 1989: Withbroe: <a href="http://articles.adsabs.harvard.edu/cgi-bin/nph-iarticle_query?1989ApJ...337L..49W&defaultprint=YES&filetype=.pdf" target="_blank">The Solar Wind Mass Flux</a>). I&#8217;m not even sure if it is resolved at the moment. But I digress!<!--more-->

<span style="line-height: 1.7em;">While continuing my readings of Wolfe&#8217;s work throughout the 1980s, I wondered if the constancy of the solar wind mass flux (given that it is indeed constant) might be an important, but largely unconsidered, feature in his ongoing analyses and interpretations&#8230; So I began playing with some solar wind data from the <a href="http://www.srl.caltech.edu/ACE/" target="_blank">Advanced Composition Explorer</a> [ACE]. To my surprise, while looking at about 2-3 months of solar wind speed and density data data (64-sec temporal resolution), I could not prove to myself that the mass flux was constant. Not even after separating it into slow, moderate, and fast categories.</span>

This weirded me out&#8230; So I started Googling and Google-Scholaring.

I found that in the older papers that mention &#8220;constant mass flux&#8221; in the solar wind, they are referring to the broad strokes, e.g., <a href="http://onlinelibrary.wiley.com/doi/10.1029/JA078i013p02028/full" target="_blank">Burlaga and Ogilvie [1973]</a> looked at median quantities over entire solar rotation periods when they uncovered this relationship.

The minute-to-minute fluctuations in the solar wind speed and density over the course of three months does not depict a constant solar wind mass flux because the mass flux is simply not a constant at this time scale. However, it might be approximately so on an hourly time scale given that<span style="line-height: 1.7em;"> Wolfe&#8217;s analyses used hourly averages of solar wind parameters. </span>

<span style="line-height: 1.7em;">The leap from the median speed and density over a solar rotation (~27-28 days) to the hourly time scale is quite large, so I decided to look at daily median quantities over the whole year of 2001. </span>

On the daily timescale, the scatterplots indeed start to look hyperbolic, as if the upstream solar wind density is inversely proportional to the speed like \(N\_{p} = \frac{A}{V\_{SW}}\). If this relationship is true, then would should expect a fairly linear relationship between \(V\_{SW}\) and \(1/N\_{p}\) since this would be equivalent to correlating \(V\_{SW}\) and \(\frac{1}{A}V\_{SW}\).

<span style="line-height: 1.7em;"><a href="http://kevin-urban.com/blog/wp-content/uploads/2015/10/daily-sw-meds__fast-only.png"><img class="alignright size-medium wp-image-373" src="http://kevin-urban.com/blog/wp-content/uploads/2015/10/daily-sw-meds__fast-only-300x242.png" alt="daily-sw-meds__fast-only" width="300" height="242" srcset="http://kevin-urban.com/blog/wp-content/uploads/2015/10/daily-sw-meds__fast-only-300x242.png 300w, http://kevin-urban.com/blog/wp-content/uploads/2015/10/daily-sw-meds__fast-only.png 610w" sizes="(max-width: 300px) 100vw, 300px" /></a>Indeed, you get a pretty straight line. The correlation increases if you scatterplot only on fast solar wind: \(N_{p}[V_{SW} > 450 \quad km/s]\) vs \(V_{SW}[V_{SW} > 450 \quad km/s]\). However, although there is a relationship, the solar wind speed only explains ~10% of the variance in the inverse density.</span>

[<img class="alignright size-medium wp-image-375" src="http://kevin-urban.com/blog/wp-content/uploads/2015/10/28-Day-Median-Solar-Wind-300x250.png" alt="28-Day-Median-Solar-Wind" width="300" height="250" srcset="http://kevin-urban.com/blog/wp-content/uploads/2015/10/28-Day-Median-Solar-Wind-300x250.png 300w, http://kevin-urban.com/blog/wp-content/uploads/2015/10/28-Day-Median-Solar-Wind.png 586w" sizes="(max-width: 300px) 100vw, 300px" />](http://kevin-urban.com/blog/wp-content/uploads/2015/10/28-Day-Median-Solar-Wind.png)Using 28-day medians, the N=A/V relationship becomes much stronger. The correlation coefficient suggests that ~33% of the variance in the inverse density is explained by the solar wind speed. However, it is only 13 data points in the single-year of 2001, so this gives us a rather large standard error on the correlation coefficient:

\(SE_{corr}=\frac{\sqrt(1-R^{2})}{\sqrt(N-2)}\sim=0.25\)

It would take further analysis to establish the details of the relationship between solar wind speed and density, e.g., a multi-year study of solar wind properties over many solar rotations. However, given just a little bit of data analysis, we were able to answer our question: Is the solar wind mass flux at Earth constant, variable, or both?

The answer, of course, is both: it depends on what time scale you&#8217;re interested in (minutes, hours, days, or solar rotation periods) and how much variation is encapsulated in one&#8217;s definition of constant (e.g., the temperature in a room might be continuously fluctuating within a tenth of a degree about 68 degrees, but most of us wouldn&#8217;t consider the temperature in the room &#8220;constant&#8221;). \(\)