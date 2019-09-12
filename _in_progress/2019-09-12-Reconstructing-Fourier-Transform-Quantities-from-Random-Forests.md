Exactly what the name says...

I want to make a few artificial signals, compute their DFT (e.g., using Multi-Taper Method), then
concoct a RF supervised learning problem.

(i) Reconstruct power spectra exactly
  - this will probably fail, but worth looking at
  - basically, if we have 1-hour windows of 1-Hz data (3600 data points), we
    would want to predict 1800 power spectra
(ii) Reconstruct power spectral bands
  - this has a better chance of working
  - for example, we can use the hydromagnetic frequency bands of interest in magnetospheric
    physics: Pc1, Pc2, Pc3, Pc4, Pc5, etc.
  - for each window, compute the DFT and compute band power by summing over relevant frequency power spectra
  - in this case, we would only need to predict, say, 5 outputs from a 3600-point time series window
  


A follow-up to this article can be using neural networks to reconstruct power spectra...  This
is likely to be an easier job than w/ RF.  One can imagine a multi-layer perceptron with only 
a few hidden layers doing the job.  
