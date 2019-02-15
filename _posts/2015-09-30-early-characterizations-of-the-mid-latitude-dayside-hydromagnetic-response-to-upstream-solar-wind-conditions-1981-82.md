---
title: 'Early characterizations of the mid-latitude, dayside hydromagnetic response to upstream solar wind conditions: 1981-82'
date: 2015-09-30T16:04:14+00:00
author: Kevin Urban
layout: post
tags: space-weather time-series data-science statistics

---
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>


In my [previous post on this topic]({{ site.baseurl }}{% post_url 2015-09-21-early-characterizations-of-the-mid-latitude-dayside-hydromagnetic-response-to-upstream-solar-wind-conditions %}), I covered two 1980 papers published by Allan Wolfe and his colleagues concerning the dependence of ground-level hydromagnetic [GLHM] energy on upstream solar wind [SW] parameters. The velocity and angle effects were both confirmed as important controllers of GLHM energy at mid-latitudes on the dayside; furthermore, the angle effect was found to be frequency-dependent, its importance diminishing from high to low frequencies. 

In the two papers I review today [[1](http://onlinelibrary.wiley.com/doi/10.1029/JA086iA09p07507/abstract), [2](http://onlinelibrary.wiley.com/doi/10.1029/JA087iA12p10425/abstract)], Wolfe and his co-authors address a few of the limitations of the previous two studies. Latitude and local time [LT] dependencies are paid attention to, as well as hemispheric effects (conjugacy study), the nightside, and additional upstream solar wind parameters. Internal sources of GLHM energy are even given a nod.

#### TLDR

In short, as of 1982, the angle effect still comes out as most important for the high-frequency (proxy Pc3) band, while the kinetic energy flux density  $$(f_{k}=\frac{1}{2}m_{p}N_{p}V_{SW}^{3})$$ stands out as most important for the proxy Pc4 and proxy Pc5 band. The authors conclude that the $$f_{k}$$ effect is likely associated with the excitement of field line resonances [FLRs] due to internal/external pressure balancing [Wu1981]. Solar wind speed ($$V_{SW}$$)&#8212;a.k.a. the velocity effect&#8212;is still found to be important, especially if not considering $$f_{k}$$ in the same regression. Furthermore, in addition to the angle effect, frequency dependence is found to be a characteristic of both the velocity and $$f_{k}$$ effects as well. For the lower frequency &#8220;proxy Pc&#8221; bands,  $$f_{k}$$ and $$V_{SW}$$ appear to have the most influence post-noon, towards dusk. Correlations are found to improve slightly when statistics are performed on the $$B_{Z}\lt 0$$ subset of $$B_{Z}$$. Lastly, the Perreault-Akasofu solar wind-magnetosphere coupling parameter, $$\epsilon$$, is found to have no significant correlations with mid-latitude, dayside GLHM energy.



After discussing the papers in a bit more detail below, I provide a few thoughts concerning an alternative interpretation of their results.<!--more-->

-----------------------------------------------

| **Terminology Refresher** | 

| **Velocity Effect**   || Increases in solar wind speed upstream of the bow shock --> increases in bulk speed in the magnetosheath downstream of the bow shock, adjacent to the magnetopause --> KHI along the magnetopause --> magnetospheric HM waves |
| **Angle Effect** ||  Decreases in IMF cone angle --> more efficient transport of HM waves from the bow shock to the magnetopause --> magnetopause transmission of HM waves --> magnetospheric HM waves |

|  **Proxy Pc Bands** || The traditional bands in magnetosphere studies include the Pc3 (10-45 sec), Pc4 (45-150 sec), and Pc5 (150-600 sec) bands. In the series of papers by Wolfe, three related bands are used. I call these the &#8220;proxy Pc bands&#8221; for reasons that should be obvious: Proxy Pc3 (30-60 sec), Proxy Pc4 (60-120 sec), and Proxy Pc5 (120-240 sec). |

-----------------------------------------------

### Expanding the Picture Painted by Previous Studies
Piecing together information on GLHM studies at mid-latitudes thus far (1981-82), and treating all HM bands equally, the two papers describe a statistical pattern of enhanced GLHM energy at the flanks (local dusk and local dawn) with a local minimum of energy near noon, which they note is consistent with the velocity effect (there is likely a [stagnation point](https://en.wikipedia.org/wiki/Stagnation_point) at noon along the Sun-Earth line, thus no KHI or KHI-associated HM waves in this region). Polarization analyses before and after local noon also appeared to support this mechanism (Lanzerotti reference).

In [Wolfe1981](http://onlinelibrary.wiley.com/doi/10.1029/JA086iA09p07507/abstract), using a single, mid-latitude (L=3.5) magnetometer site during boreal (northern) summer dates when the IMP J spacecraft was beyond bow shock in the solar wind, the authors look for a clear diurnal pattern in the mid-latitude, GLHM energy in three frequency bands (Wolfe&#8217;s proxy Pc3-5 bands), but come up empty-handed, suggesting that the statistical results, while not incorrect, might not accurately characterize any one day in particular. Looking more closely at the dayside variations, they do note that the lower frequency bands (proxy Pc4 and proxy Pc5) co-vary with each other day to day and, in general, tend to increase in power from the morning towards the afternoon, while the highest frequency band (proxy Pc3) varies independently of the lower frequencies and tends to decrease over the dayside hours.

These observations are in contrast to the  peak-lull-peak picture of GLHM energy as one goes from dawn to noon to dusk. This might be due to the statistics as suggested above (&#8220;right on average, but wrong all the time&#8221;), or because the peak-lull-peak picture does not take into account that the coherence and amplitude of GLHM energy is frequency-dependent and that, for each frequency band, there might be differing local time [LT] dependencies. The authors therefore emphasize the importance of delineating frequency bands when discussing hydromagnetic energy&#8212;that an overall picture cannot be painted by overlaying bits and pieces of information from various frequency bands. (In the coming years, this issue is dealt with using short-time Fourier transform and wavelet analyses.)

[Wolfe1981](http://onlinelibrary.wiley.com/doi/10.1029/JA086iA09p07507/abstract) confirms that at any given site, HM spectra follow a power-law dependence on frequency (higher frequencies hold substantially less power than the lower frequencies in the geomagnetic field). Using three nearly-but-not-quite conjugate sites (L=3.5 North, 4.0 South , 4.4 North) during a week of equinox, [Wolfe1982](http://onlinelibrary.wiley.com/doi/10.1029/JA087iA12p10425/abstract) confirms that the HM spectra at conjugate stations take on similar shapes and slopes, but notes that, in general, GLHM energy is directly dependent on latitude&#8212;higher latitudes measure higher levels of energy (in agreement with Surkan and Lanzerotti [1974]). Furthermore, [Wolfe1981](http://onlinelibrary.wiley.com/doi/10.1029/JA086iA09p07507/abstract) finds that energy levels across all three proxy Pc bands are typically higher on disturbed days than quiet days (as measured by $$ \Sigma Kp $$, but cannot establish a statistically significant relationship between the Kp index and GLHM energy&#8212;also in agreement with previous studies. In terms of the HM spectra, this tells us that energy decreases as one goes higher in frequency or lower in latitude, and is likely to decrease with Kp, despite not having a well-defined relationship with Kp.

When investigating the relationship between GLHM energy and upstream solar wind parameters, many previous studies looked primarily into the velocity and angle effects, focusing heavily on the dayside. The velocity effect was found to be important across frequency bands and likely having most control at the dawn/dusk flanks, consistent with reports of GLHM energy. The angle effect had been shown to be a global phenomenon, taking place simultaneously at largely separated stations. This global effect is likely due to the structuring of HM energy via FLRs.

[Wolfe1981](http://onlinelibrary.wiley.com/doi/10.1029/JA086iA09p07507/abstract) introduces many additional solar wind parameters into the mix, including 

* mass flux $$(m_{p}N\_{p}V_{SW})$$ 
* momentum flux $$(m_{p}N_{p}V_{SW}^{2})$$ 
* kinetic energy flux density $$(f_{k}=\frac{1}{2}m_{p}N_{p}V_{SW}^{3})$$ 
* Perreault-Akasofu coupling parameter $$(\epsilon=L_{0}^{2}V_{SW}sin^{4}\left(\frac{\theta_{clock}}{2}\right))$$ 

In the PA parameter, $$L_{0}^{2}$$ stands in as an &#8220;interaction area&#8221; between the solar wind and the magnetosphere and $$\theta_{clock}=atan2(B_{Y},B_{X})$$. Mass flux and momentum flux are not further commented upon after mentioning that they were investigated (more on this below).

[Wolfe1981](http://onlinelibrary.wiley.com/doi/10.1029/JA086iA09p07507/abstract) contrasts the solar wind control of GLHM energy between the dayside and nightside by applying single-variable (simple) linear regression to each pairing of solar wind parameter and GLHM frequency band. Higher correlations and significance is found on the dayside for most solar wind parameters. The angle effect is significant for the highest frequency band on the dayside, but is essentially nonexistent and insignificant for the lower frequency bands and all bands on the nightside. $$V_{SW}$$ and $$f_{k}$$ have the highest correlations with GLHM energy and are significant for all bands on both the dayside and nightside, while $$\epsilon$$ is not significant on either side for any band.With this in mind, the authors look no further into the nightside in the current work, noting that geomagnetic tail parameters likely control the nightside GLHM energy.

[Wolfe1981](http://onlinelibrary.wiley.com/doi/10.1029/JA086iA09p07507/abstract) subsequently performs various permutations of stepwise multiple linear regression [SMLR] analysis, showing support that $$f_{k}$$ is the most important parameter across all bands on the dayside, whereas $$V_{SW}$$ emerges as the most important if one does not include \(f_{k}\) in the regression. SMLR analyses also strongly support the unimportance of $$\epsilon$$). Fearing their assessment of $$\epsilon$$ might be wrong (perhaps the days studied in [Wolfe1981](http://onlinelibrary.wiley.com/doi/10.1029/JA086iA09p07507/abstract) were too quiet, or perhaps the insignificance was an effect local to the magnetometer site), [Wolfe1982](http://onlinelibrary.wiley.com/doi/10.1029/JA087iA12p10425/abstract) further establishes the importance of $$f_{k}$$ and insignificance of $$\epsilon$$ using SMLR analyses on multiple mid-latitude magnetometers in both hemispheres. They further check if these effects are enhanced when $$B_{Z} \lt 0$$: $$f_{k}$$ is, $$\epsilon$$ is not.

However, despite all evidence pointing towards \(\epsilon\) having no value as a predictive parameter for GLHM energy, [Wolfe1982](http://onlinelibrary.wiley.com/doi/10.1029/JA087iA12p10425/abstract) hedges its bets, stating that \(\epsilon\) might not be significant in this study since previous studies had established that it is important at much higher energies&#8212;thus, \(\epsilon\) might have a step-function-like threshold it must pass before it becomes significant.

Having the highest correlations, $$f_{k}$$ and $$V_{SW}$$ are further analyzed as a function of local time: the hourly sampled GLHM time series for each band over the 28 days studied in [Wolfe1981](http://onlinelibrary.wiley.com/doi/10.1029/JA086iA09p07507/abstract) are broken into 3*24 daily-sampled time series at 24 independent bins of local time; the authors, however, only look at the 12 dayside LT time series. Although a mere 28 data points appears to be a small amount of data, given that each data point in one of these time series is separated from its neighbors by 24 hours, the authors argue that there is likely no contamination from autocorrelation and repeated measurements when estimating the confidence levels. In fact, [Wolfe1981](http://onlinelibrary.wiley.com/doi/10.1029/JA086iA09p07507/abstract) shows afternoon peaks above the 99.9% confidence level in the various frequency bands, suggesting that the solar wind controls the dayside best in the afternoon sector. [Wolfe1982](http://onlinelibrary.wiley.com/doi/10.1029/JA087iA12p10425/abstract) shows a corresponding bump in afternoon GLHM energy at all three magnetometer sites, thought to be due to an FLR.

The LT correlations between $$V\_{SW}$$ and the higher frequency bands sharply drop near noon, suggesting that GLHM energy at noon is not controlled by a KHI mechanism, which is consistent with a stagnation point in the flow near noon. Lastly, the authors find the proxy Pc5 band to better correlate with $$f_{k}$$ than $$V_{SW}$$, while the proxy Pc3 band does just the opposite, pointing towards a frequency dependence in both effects.

In addition to external parameters, heretofore, researchers had also looked into internal sources of GLHM energy, such as variations in the ionospheric electrojet. [Wolfe1981](http://onlinelibrary.wiley.com/doi/10.1029/JA086iA09p07507/abstract) notes that peaks in GLHM energy often correspond with peaks in $$f_{k}$$, but just as often do not, suggesting multiple sources of GLHM energy, including internal or externally-triggered internal sources; however, internal sources were not considered in the various analyses. To lend confidence that the observed correlations were more likely due to external driving of the magnetosphere from upstream solar wind parameter than due to localized sources, [Wolfe1982](http://onlinelibrary.wiley.com/doi/10.1029/JA087iA12p10425/abstract) looked at multiple conjugate sites in both hemispheres.

<a name="polynomial"></a>
### Solar Wind Speed as the Most Important Parameter: Polynomial Regression
  
In [Wolfe1981](http://onlinelibrary.wiley.com/doi/10.1029/JA086iA09p07507/abstract), the authors mention that they looked at scatter plots and correlations of many more upstream parameters than they actually show or decide to go forward with. Some of upstream parameters, like mass flux and momentum flux, peaked my interest, but were not further mentioned or commented on throughout the paper. My assumption is that the authors found these parameters to not have high correlations with GLHM energy, prompting them to look no further&#8212;at least for the time being. However, there are some good reasons why I think the paper should have further addressed these variables.

Consider the following facts identified throughout the four Wolfe papers I&#8217;ve now covered:

  1. $$V_{SW}$$ is considered an important upstream variable
  2. $$N_{p}$$ is found to be largely unimportant (e.g., [Wolfe1982](http://onlinelibrary.wiley.com/doi/10.1029/JA087iA12p10425/abstract))
  3. There is often a high correlation between $$V_{SW}$$ and $$N_{p}$$ (see Wolfe1980)
  4. $$f_{k}=\frac{1}{2}m_{p}N_{p}V_{SW}^{3}$$ is deemed the most important upstream variable ([Wolfe1981](http://onlinelibrary.wiley.com/doi/10.1029/JA086iA09p07507/abstract), [Wolfe1982](http://onlinelibrary.wiley.com/doi/10.1029/JA087iA12p10425/abstract))
  5. Since mass flux and momentum flux were not scrutinized, I assume the authors found them to have low correlations.

If $$V_{SW}$$ is important, and $$N_{p}$$ is not important, one might imagine that the importance of mass flux ($$m_{p}N_{p}V_{SW} $$) could go either way&#8212;so what makes the mass flux unimportant?

[Wolfe1980b](http://onlinelibrary.wiley.com/doi/10.1029/JA085iA11p05977/abstract) shows a strong relationship between solar wind speed and density in which density decays nonlinearly as a function of solar wind speed. To my mind, the scatterplot of $$V_{SW}$$ vs $$N_{p}$$ in [Wolfe1980b](http://onlinelibrary.wiley.com/doi/10.1029/JA085iA11p05977/abstract) looks fairly hyperbolic, as if $$N\_{p} = alpha/V_{SW}$$ In the series of papers, it is repeatedly shown that density by itself is not an important upstream parameter, but that $$V_{SW}$$ is, especially when not also considering the influence of $$f_{k}$$

Identifying $$N_{p}$$ as $$\alpha V_{SW}^{-1}$$ it shouldn&#8217;t be surprising that $$N_{p}$$ does not measure as having a strong linear relationship with GLHM energy. Furthermore, given this relationship between $$V_{SW}$$ and $$N_{p}$$ it is not surprising that the authors deemed mass flux ( $$ m_{p}N_{p}V_{SW} $$ ) to be an unimportant upstream parameter in controlling GLHM energy since the relationship suggests that mass flux in the solar wind is roughly constant:

  Mass Flux = $$m_{p}N_{p}V_{SW} = \alpha m_{p} = A$$

The identified relationship between $$N_{p}$$ and $$V_{SW}$$ tells us something further about $$f_{k}$$: given the roughly constant nature of mass flux in the solar wind, importance is mistakenly ascribed to $$f_{k}$$, which in this circumstance is a stand-in for  $$V_{SW}^{2}$$:

$$f_{k} = \frac{1}{2} m_{p}N_{p}V_{SW}^{3}=\frac{1}{2}AV_{SW}^{2} = CV_{SW}^{2}$$

If mass flux results in a constant and $$f_{k}$$ is actually just a stand-in for $$V_{SW}^{2}$$ then we should also expect that momentum flux would serve as a stand-in for $$V_{SW}$$ and have nearly the same correlative relationship with GLHM energy that the authors found with $$V_{SW}$$

<p style="text-align: center;">
  Momentum Flux = \(m_{p}N_{p}V_{SW}^{2}\) = \(m_{p}AV_{SW}\) = \(BV_{SW}\)
</p>

<p style="text-align: left;">
  However, the authors also did not further investigate momentum flux. At the least, I would think they found it to be highly correlated with GLHM energy and with \(V_{SW}\), thus making it a confounding variable having little-to-no predictive power outside of its observed dependence on \(V_{SW}\). But, unfortunately, the authors did not mention why they discarded momentum flux in their investigations.
</p>

<p style="text-align: left;">
  For a regression, these considerations suggest that one might find the best multiple regression on a polynomial in \(V_{SW}\):
</p>

<p style="text-align: center;">
  \(\\ log(GLHM)=A+BV_{SW}+CV_{SW}^{2}+DV_{SW}^{3}\)
</p>

I leave in $$V_{SW}^{3}$$ as a possibility just in case, although we did find that $$ V_{SW} $$ and $$V_{SW}^{2}$$ are the most important upstream variables given the solar wind mass flux is roughly constant.

**On the Role of Simulation in an Observational Science**
  
One last note: a constant mass flux in the solar wind makes it fairly difficult to tell the difference between $$f_{k}$$ and $$V_{SW}^{2}$$ in terms of correlation with GLHM energy and which has more predictive power. This is the exact type of situation where modeling can lend some interesting insights: since geophysical studies like this rely on observation and have limitations on the types of controlled experiments that can be performed, computational modeling can serve as a stand-in for experiments. For example, one can hold density constant as one tweaks the solar wind speed (and vice versa), resulting in a non-constant mass flux and ultimately a way to distinguish between $$f_{k}$$ and $$V_{SW}^{2}$$

Seth Claudpierre has some nice papers on this way later in the HM/ULF timeline  (e.g., [Claudepierre2008](http://onlinelibrary.wiley.com/doi/10.1029/2007JA012890/full), [Claudepierre2009](http://onlinelibrary.wiley.com/doi/10.1029/2009GL039045/full), and [Claudepierre2010](http://onlinelibrary.wiley.com/doi/10.1029/2010JA015399/full)). 
