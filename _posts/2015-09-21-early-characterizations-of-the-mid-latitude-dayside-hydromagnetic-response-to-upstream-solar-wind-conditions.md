---
title: 'Early characterizations of the mid-latitude, dayside hydromagnetic response to upstream solar wind conditions: 1980'
layout: post
date: 2015-09-21T22:27:58+00:00
author: Kevin Urban
tags: space-weather time-series data-science statistical-modeling
---
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script>

The solar wind speeds up. It slows down. It blows by Earth at a breath-taking million miles per mile on a lethargic day. Double that when it&#8217;s feeling frisky.

As the quasi-stable electrical current systems and plasma regions in the Sun&#8217;s corona slowly vary, associated low-frequency magnetic disturbances propagate out into the solar system, not at the speed of light in vacuum (as for example disturbances in the optical band might do), but more-or-less in sync with the flow of the extremely conductive solar wind plasma. This solar wind-associated magnetic field is often called the interplanetary magnetic field [IMF].

Circa 1980, when Allan Wolfe wrote the two papers I am discussing today (see [Wolfe1980a](http://onlinelibrary.wiley.com/doi/10.1029/JA085iA01p00114/full) and [Wolfe1980b](http://onlinelibrary.wiley.com/doi/10.1029/JA085iA11p05977/abstract) below), space scientists were trying to establish what connections, if any, existed between interplanetary parameters upstream in the solar wind and hydromagnetic [HM] energy measured by magnetometers on the ground (a &#8220;hydromagnetic wave&#8221; is the cooler, old school term for what is more often today referred to as a  &#8220;[magnetohydrodynamic](https://en.wikipedia.org/wiki/Magnetohydrodynamics) wave&#8221;). The solar wind speed (&#8220;velocity effect&#8221;) and the direction of the IMF (&#8220;angle effect&#8221;) were theoretically justified as major factors in controlling the hydromagnetic energy levels at Earth&#8217;s surface. In order to verify or nullify these hypotheses, the task was to get a spacecraft just ahead of the Earth in the solar wind so observational researchers could analyze the data obtained in this region with data obtained on the ground.<!--more-->

#### The Velocity Effect
  
The idea goes like this: increases in the solar wind speed just upstream of Earth&#8217;s bow shock induce faster plasma flows earthward of the bow shock in the magnetosheath [Dryer1966, Spreiter1966]. The magnetosheath plasma flows antisunward, continuously grazing by Earth&#8217;s magnetopause. Increases the speed of the magnetosheath plasma relative to the magnetopause are associated with increased probability of inducing a Kelvin-Helmholtz instability [KHI] at the magnetopause [Atkinson1966, Southwood1974], which is associated with the generation of hydromagnetic waves inside the magnetosphere. The generated HM waves, in turn, often excite field line resonances [FLRs], ultimately enhancing the hydromagnetic activity at ground level [Radoski1966, Chen1974, Southwood1974].

#### The Angle Effect
  
The bow shock is&#8212;you guessed it&#8212;a type of [shock](https://en.wikipedia.org/wiki/Shock_wave). It is upstream of Earth&#8217;s magnetosphere in the solar wind (&#8220;sunward of the magnetosphere&#8221;). Hydromagnetic waves are generated at this shock and, as the hypothesis goes [Greenstadt1973], these waves are more readily transported to Earth&#8217;s magnetopause when the IMF is predominately lying in the ecliptic plane&#8212;that is, when the IMF has little-to-no north or south component. To measure this, we use the &#8220;cone angle&#8221; of the IMF, defined as 

$$ acos(|Bx|/B) $$

where Bx is the magnetic field component along the sunward/antisunward axis. The idea is that if the waves are more readily transported to the magnetopause, then if they can transmit through the magnetopause [Fejer1963, Wolfe1975], they can in turn excite FLRs inside the magnetosphere and cause variations in the hydromagnetic energy levels measured on the ground [Radoski1966, Chen1974, Southwood1974].

## The Work of Wolfe
#### Study #1:  
In an attempt to test the &#8220;velocity effect&#8221; and &#8220;angle effect&#8221; hypotheses, in [Wolfe1980a](http://onlinelibrary.wiley.com/doi/10.1029/JA085iA01p00114/full), the authors use recently obtained upstream solar wind data obtained by the spacecraft IMP J (also known as [Imp 8](http://nssdc.gsfc.nasa.gov/nmc/spacecraftDisplay.do?id=1973-078A)) to compare with dayside ground-based magnetometer time series data (defined as any of the 1-hour integer-to-integer intervals between 0600-1800 local time) recorded at a mid-latitude site (located in New Hampshire) during two time periods when IMP J was upstream of the bow shock.
  
<figure>
<img src="/images/wolfe1980a_speed.png" alt="wolfe1980a_speed"  width="600vw"/>
</figure>

During the first interval, the authors show that for three hydromagnetic frequency bands (periodicity bands of 30.7-60, 60-120, and 120-240 seconds, which are proxies for the more traditionally defined Pc3 [10-45 s], Pc4 [45-150 s], and Pc5 [150-600] bands), the solar wind speed appears to be the dominant independent variable. However, he goes on to show that during this time interval, the IMF cone angle is correlated with the solar wind speed. Therefore, by employing a regression analysis, any detectable relationship between the solar wind speed or the IMF cone angle with the mid-latitude ground-level HM energy is [confounded](https://en.wikipedia.org/wiki/Confounding)&#8212;that is, if and when these two upstream solar wind variables are correlated, it isn&#8217;t clear if the detected relationships with ground-level HM energy are exclusively due to the velocity effect, the angle effect, or both the velocity and the angle effect.

<figure>
<img src="/images/wolfe1980a_angle.png" alt="wolfe1980a_angle" width="600vw">
</figure>

To address this, Wolfe shows that during the second interval of study there is no such correlation between the IMF direction and solar wind speed, and so the two variables can be regarded as independent. In these circumstances, he is again able to present a strong case in favor of the velocity effect. However, the case for the angle effect is more subtle: Wolfe finds that there is a frequency dependence associated with the angle effect. The analysis shows that only the highest frequency band (the &#8220;proxy Pc3&#8221; band) has energy strongly controlled by the IMF cone angle. Wolfe is unable to explain this beyond speculation (e.g., natural band-pass filtering at the shock).

#### Study #2
In [Wolfe1980b](http://onlinelibrary.wiley.com/doi/10.1029/JA085iA11p05977/abstract), the dayside ground-based magnetometer data set at mid-latitude site in New Hampshire is expanded to cover four time intervals when IMP J is beyond the bow shock in the upstream solar wind.  The data is collated into one large-by-the-day&#8217;s-standards data set to mine significant overall relationships from.

In this study, Wolfe wants to further quantify relative importance of the velocity and angle effects in controlling the ground-level dayside hydromagnetic energy at mid-latitudes, but also wants to know if these effects are possibly due to other underlying parameters in the solar wind. To address this, Wolfe scouts out [confounding variables](https://en.wikipedia.org/wiki/Confounding)&#8212;that is, to uncover solar wind variables that are not independent of each other&#8212;by looking at scatter plots and computing the correlations between 8 solar wind parameters:

<!-- Vim doesn't understand this as a MathJax codeblock, so "_" is visualized as making-->
<!-- the text emphatic.  This looks annoying while editing, so if the "_" count is not -->
<!-- even, just find a way to make it so! -->
* Bulk Speed:  $$V_{SW}$$ 
* Cone Angle:  $$\theta_{xB}$$
* Magnetic Field Components $$B_{X}, B_{Y}, B_{Z}$$
* Magetic Field Strength: $$B_{}$$
* Proton Number Density: $$N_{p}$$
* Proton Temperature: $$T_{p}$$


Wolfe shows an extremely strong correlation between temperature and speed, no correlation between speed and cone angle, and a strong nonlinear relationship between speed and density. These results prompt the question: Is it possible that the velocity effect is in fact not an effect of velocity, but instead due to solar wind temperature or density? Looking at the scatterplot and correlation coefficient of solar wind temperature versus ground-level HM energy, one would be very inclined to posit a causal relationship.

To identify and quantify the solar wind parameters important in controlling ground-level hydromagnetic energy, Wolfe implements a sequence of multiple linear regression [MLR] analyses ([stepwise MLR](https://en.wikipedia.org/wiki/Stepwise_regression)) on the 8 solar wind parameters. To indicate the importance of each variable in a given MLR, the T Ratio is used (aka, [t-statistic](https://en.wikipedia.org/wiki/T-statistic))&#8212;that is, the ratio between the coefficients (slopes) of each (presumably) independent variable and their associated errors. The larger the T Ratios, the more important Wolfe considers the variable in the fit.

<figure>
<img src="/images/wolfe1980b_MLRs.png" alt="wolfe1980b_MLRs" width="600vw"><figcaption class="wp-caption-text">Examples of a [stepwise MLR](#MLR) for each frequency band (click to enlarge). For each of the three boxes (Figs. 4-6), the leftmost column shows the results of MLR using all 8 solar wind variables. Each subsequent column is the result of MLR after removing the bottom-most variable from previous column. The shadings (4 gradations from light to dark) indicate the value of the T Ratio (from low to high); no shading corresponds to &#8220;not applicable&#8221; (i.e., the bottom-right triangle of the matrix has no shading).</figcaption></figure> 

This type of analysis allows Wolfe to show justification that the velocity effect is very likely due to velocity&#8212;not temperature or density. The high correlation found between the solar wind temperature and the ground-level HM energy&#8212;judging by T Ratios and various sequences of stepwise MLR&#8212;is likely due to the high correlation between the temperature and speed. The importance of solar wind density is not ruled out: for most of the MLR analyses, its inclusion causes a sharp reduction in residual error and steep rise in the correlation. However, its predictive power pales in comparison to solar wind speed, and given the relationship it was shown to have with speed, at least some of its predictive power is likely due to speed. Therefore, in the current study, Wolfe looks no further into the &#8220;density effect.&#8221;

<figure>
<img src="/images/wolfe1980b_empirical-equations.png" alt="wolfe1980b_empirical-equations" width="300vw"/>
</figure>

Ultimately, the study further demonstrates and justifies the velocity and angle effects, concluding that they are indeed the dominant solar parameters controlling HM energy levels on the ground, noting the possible importance of density. The study more clearly shows that the angle effect has a frequency dependence, but adds no explanation beyond further speculations as to the cause. It concludes with equations between the solar wind speed, cone angle, and ground-level HM energy for the three inspected frequency bands, which can be used in either direction &#8212; as ground-based solar wind probes or space-based estimates of mid-latitude ground measurements on the dayside!

* * *

<span style="text-decoration: underline;"><strong>Limitations of the Study</strong></span>
  
Having noted what Wolfe&#8217;s studies established, it is just as important to note what Wolfe did not establish or look at in these two studies. The list below is in no way a criticism of these two papers&#8212;in fact, much of what is listed below was carried out by Wolfe and his contemporaries in the years soon after (or, if not, the decades thereafter!). This section serves to demonstrate ways in which an observational science moves forward.

**(1) Polarization**
  
Wolfe only analyses the effect that the solar wind parameters have on the magnetic north component. Being a vectorial quantity, there are two further components that one can look at&#8212;the magnetic east and vertical components.

**(2) Latitude dependence of the MLR analyses**: These studies looked at a single mid-latitude magnetometer to characterize the mid-latitude dayside hydromagnetic response to upstream solar wind parameters. One might be interested in comparing the MLR analyses of multiple mid-latitude magnetometers in some span (e.g., 30-55* magnetic latitude). Are the dependencies on solar wind speed and cone angle the same in this mid-latitude interval? If not, then why not? For example, this could shed light on the perplexing frequency dependence of ground-level hydromagnetic energy on cone angle, which was not understood in this study.

**(3) Local time dependence: **As of 2015, we have access to years of upstream solar wind data thanks to missions like ACE and Wind. But at the time of these two studies, Wolfe had to find time periods in which IMP J was beyond the bow shock, and so had a limited collection of several-day spans of upstream solar wind data. Although Wolfe was aware that a local time MLR analysis would have been ideal (e.g., 1-hour bins between 0600 and 1800 local time), having only 336 1-hour dayside bins means that, e.g., for 1-hour local time bins, there would only be 28 solar wind data points to compare to for each bin. (Note: in a follow-up paper, Wolfe and co-authors actually do just this, noting that it is enough data since for each time bin there is ~24 hours between data points and little-to-no risk of autocorrelation or repeat measurements affecting the confidence levels.)

**(4) Seasonal Dependence**
  
Note that in both papers, the data intervals only cover summertime dates. One might further ask if the established relationships vary by season&#8212;summer, winter, and the equinoxes.

**(5) Hemispheric Asymmetries**
  
Since southern winter occurs during northern summer, this is similar to investigating the seasonal dependencies of the relationships found between the upstream solar wind parameters and the ground-level HM energy. However, it is still viable to ask if even the seasonal dependencies vary by hemisphere.

**(6) Time resolution:** Wolfe primarily looked at 1-hour time bins, checking his results afterwards with 6-hour time bins to test against distortions of the statistical significant due to autocorrelations in the 1-hour time steps. Looking at higher time resolutions was not considered due to autocorrelation considerations, which can be dealt with, but was slight overkill for the current studies. Better time resolution, for example (as Wolfe noted), could help understand the frequency dependence of the hydromagnetic spectra on cone angle since cone angle can change many times over in a given hour.

**(7) Space weather dependence:** that is, how do the observed relationships change when looking at times when corotating interaction regions [CIRs] are brushing by the Earth versus coronal mass ejections versus &#8220;uneventful&#8221; solar wind conditions? Again, due to the lack of solar wind data circa 1980, this was not likely a question that could be reasonably be answered. Also, given that researchers were still establishing basic connections, this type of breakdown-by-event-type would have been like putting on your shoes before your socks.

**(8) Nightside dependence:** the nightside is an obvious &#8220;next study&#8221; since Wolfe&#8217;s two 1980 papers only covered the dayside. Even if one suspected that nightside activity was not directly driven by upstream solar wind conditions, it is important to check and see.

**(9) Other solar wind parameters:** Wolfe looked at a bunch, but one could always look at more, e.g., the ratio of helium ions to hydrogen or the clock angle.

**(10) Ionospheric parameters:** The ionosphere acts at a filter between the happenings in the magnetosphere and the activity observed on the ground, so it is straightforward that changes in the ionosphere (electron density, conductance, etc) serve to represent different filters acting on an incoming signal. However, if a small data set is the problem, such a study represents possibly unreasonable constraints given that the researcher is now also required to restrict the study to times when a satellite was overhead and able to record such ionospheric parameters.

**(11) Additional hydromagnetic frequency bands: **Wolfe and his colleagues looked at what I call proxy Pc bands: a proxy Pc3 band, a proxy Pc4 band, and a proxy for the high-frequency end of the Pc5 band. Looking at the full Pc5 band, and even the Pc6 band, would be nice, although using a short-time Fourier transform technique might be too restrictive in that the longer window that is necessary for the Pc5 and Pc6 bands might not localize the dynamics of the Pc3 band well. A multi-resolution wavelet analysis could be performed to overcome this.

* * *

<span style="text-decoration: underline;"><strong>More on Wolfe&#8217;s Data Analysis</strong></span>
  
I first wrote this incorporating the analysis aspects into the main narrative, but this impeded the flow of physical ideas with technical jargon from statistics and time series analysis. I figured I would save it for the end for more ambitious readers.

**The Logarithm Of Energy**
  
Note that all regressions employ the logarithm of energy as the dependent variable. The actual relationships between the solar wind and the mid-latitude, ground-level HM energy are nonlinear.

**Estimating Ground-Level HM Energy**
  
In both studies, to estimate the dayside hydromagnetic energy on the ground, Wolfe first splits up the data set into 1-hour segments, discarding any of the segments on the nightside (1800-0600). Each of the remaining data segments are equally considered &#8220;dayside&#8221; segments, not explicitly differentiating effects that might be dependent on local time. For each dayside segment in his data set, he then computes the power spectral density of the magnetic north component (ignoring the magnetic east and vertical components during these early studies) by tapering each segment with a Thomson window and applying an FFT [[Thomson1976](http://www.sciencedirect.com/science/article/pii/0031920176900509)]; this provides estimates of the HM energy density [nT^2/Hz]. Since Wolfe is interested in how the upstream solar wind controls energy contained in distinct frequency bands, he defines three bands of interest and band-integrates the spectral density. Note that while this provides a measure of energy in these bands, it ignores if the energy is due to an incoherent &#8220;power law&#8221; spectrum of geomagnetic noise (colored noise), or if any coherent waves are present (significant bumps above the background noise).

**Computing Hourly Averages of Cone Angle**
  
The cone angle is defined as \(\theta\_{xB}=arccos(|B\_{X}|/B)\). The IMP J magnetic field data has a temporal resolution of about 15.36 seconds. However, Wolfe downsamples the original time series (aka [decimation](https://en.wikipedia.org/wiki/Decimation_(signal_processing))) of each magnetic field component into a new time series with 1-hour temporal resolution&#8212;of which the simplest way is to take averages of consecutive, independent 1-hour time bins. The cone angle itself is not downsampled itself; the &#8220;hourly average&#8221; of the cone angle is defined in terms of the decimated magnetic field components.

**Hedging Against Autocorrelation**
  
In both studies, to further establish the results, Wolfe downsamples each of the data sets to 6-hour temporal resolution and performs further regression analyses. He does this to hedge against false confidence in the significance of his results due to autocorrelations present in the 1-hour resolution data. The additional analyses provide further justification of the initial findings. In the second paper [[Wolfe1980b](http://onlinelibrary.wiley.com/doi/10.1029/JA085iA11p05977/abstract)], both the 1-hour and 6-hour time resolved data sets have enough data points to establish correlations with significances at the 99.9% level, assuming no autocorrelation. (For more on the effect of autocorrelation on estimates of correlation and its significance in a geophysical setting, see [Carter1977](http://www.sciencedirect.com/science/article/pii/0032063378900624).)

<a name="MLR"></a>
  
**Stepwise Multiple Linear Regression**
  
The recipe goes like this:
  
_1. Choose a sequence of presumably independent parameters (they need not actually be independent, but at the beginning of the analysis we pretend they are)._
  
 _2. Perform MLR and note the correlation coefficient, the residual standard error, and the coefficient and error of each supposedly independent parameter._
  
 _3. Remove the last variable from the sequence chosen in (1)._
  
 _4. Repeat (2) and (3) until there is only one variable left._
  
 _5. Repeat (1)-(4) on additional permutations of the parameter set._

* * *

**Main References**

<table style="font-size: small;">
  <tr>
    <td>
      Wolfe1980a: A. Wolfe, L. J. Lanzerotti, and C. G. Maclennan: <a href="http://onlinelibrary.wiley.com/doi/10.1029/JA085iA01p00114/full">Dependence of Hydromagnetic Energy Spectra on Solar Wind Velocity and Interplanetary Magnetic Field Direction</a>
    </td>
  </tr>
  
  <tr>
    <td>
      Wolfe1980b: A. Wolfe: <a href="http://onlinelibrary.wiley.com/doi/10.1029/JA085iA11p05977/abstract">Dependence of Mid-Latitude Hydromagnetic Energy Spectra on Solar Wind Velocity and Interplanetary Magnetic Field Direction<br /> </a>
    </td>
  </tr>
</table>

**Other References**

<table style="font-size: small;" border="1">
  <tr>
    <td>
      Atkinson1966: <a href="http://www.sciencedirect.com/science/article/pii/0012821X66901129">Surface waves on the magnetospheric boundary as a possible origin of long period geomagnetic pulsations</a>
    </td>
    
    <td>
      Carter1977: <a href="http://www.sciencedirect.com/science/article/pii/0032063378900624">An important statistical consideration, and the effect of the interplanetary magnetic field on the quiet time variation of the geomaganetic field</a>
    </td>
    
    <td>
      Chen1974: <a href="http://onlinelibrary.wiley.com/doi/10.1029/JA079i007p01024/full">A theory of long-period magnetic pulsations, 1: Steady state excitation of field line resonance</a>
    </td>
  </tr>
  
  <tr>
    <td>
      Dryer1966: <a href="http://arc.aiaa.org/doi/abs/10.2514/3.3425?journalCode=aiaaj">The magnetogasdynamic boundary condition for a self-consistent solution to the closed magnetopause</a>
    </td>
    
    <td>
      Fejer1963: <a href="http://scitation.aip.org/content/aip/journal/pof1/6/4/10.1063/1.1706765">Hydromagnetic reflection and refraction at a fluid velocity discontinuity</a>
    </td>
    
    <td>
      Greenstadt1973: <a href="http://ntrs.nasa.gov/search.jsp?R=19740048171">Field-determined oscillations in the magnetosheath as possible sources of medium period daytime micropulsations</a>
    </td>
  </tr>
  
  <tr>
    <td>
      Radoski1966: <a href="http://onlinelibrary.wiley.com/doi/10.1029/JZ071i007p01891/full">Magnetic toroidal resonances and vibrating field lines</a>
    </td>
    
    <td>
      Southwood1968: <a href="http://www.sciencedirect.com/science/article/pii/0032063368901001">The hydromagnetic stability of the magnetospheric boundary</a>
    </td>
    
    <td>
      Southwood1974: <a href="http://www.sciencedirect.com/science/article/pii/0032063374900786">Some features of field line resonances in the magnetosphere</a>
    </td>
  </tr>
  
  <tr>
    <td>
      Spreiter1966: <a href="http://www.sciencedirect.com/science/article/pii/0032063366901243">Hydromagnetic flow around the magnetosphere</a>
    </td>
    
    <td>
      Thomson1976: <a href="http://www.sciencedirect.com/science/article/pii/0031920176900509">Spectral and windowing techniques in power spectral analyses of geomagnetic data</a>
    </td>
    
    <td>
      Wolfe1975: <a href="http://onlinelibrary.wiley.com/doi/10.1029/JA080i013p01764/abstract">MHD wave transmission and production near the magnetopause</a>
    </td>
  </tr>
</table>

&nbsp;
