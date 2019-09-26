

There's AR, MA, ARMA, ARIM, FARIMA, ARCH, GARCH, and more (didn't even being to list nonparametric
approaches).  These things stem from statistics, econometrics, and digital signal processing.  How and
where does ML come in for time series forecasting?  Obviously there are RNNs.  But what else?  RFs 
basically suck at time series forecasting, at least if you're naive about it (they are great interpolators,
but terrible extrapolaters, so if you have a linear trend that continues past the historical/training
data, you're basically screwed -- at least without lots of pre- and post-processing external to the RF
itself).

So what else is there?

2012: Bontempi et al: [Machine learning strategies for time series forecasting](https://scholar.google.com/scholar?hl=en&as_sdt=0%2C33&q=Machine+Learning+Strategies+for+Time+Series+Forecasting&btnG=)
