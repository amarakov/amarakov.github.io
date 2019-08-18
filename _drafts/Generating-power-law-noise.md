---
layout: default
title: On Generating Power Law Noise
description: Some thoughts/ details about J. Timmer and M. Konig's paper: On Generating Power Law Noise
---

I work in superconducting electronics. This almost always means Josephson junctions and SQUIDs are involved, and it also means that magnetic flux noise is the bane of my existence. Indeed, I gave a short talk to this end at a conference (ISEC 2019). Those of you who deal with qubits other than the venerable transmon, are likely battling charge noise AND flux noise. I don't mean to suggest that superconducting electronics as a field has a monopoly on noise (we don't), but it is my primary focus. Needless to say, pretty much everyone with a quantitative job runs in these problems. 

SQUIDs in particular exhibit something that is colloquially called 'pink noise' (as opposed to familiar 'white noise' or the less commonly used 'red noise'). I think these names are dumb and confusing, because these are all manifestations of a single type: Power Law Noise.

$$ S(\omega) = A^2/f^{\alpha}   (1) $$

Pink noise, has a power $$ \alpha = 1 $$, white noise has power $$ \alpha = 0 $$ and 'red noise' has power $$ \alpha = 2 $$. We also call pink noise '1/f noise'. 

A standard way of characterizing SQUID loops for sensing applications (magnetic fields usually, but that's not all they're good for!) is by measuring the noise power spectral density of the device. That is to say, measuring how much noise the SQUID sees in each decade of frequency. It's important to note that this is <em> always </em> represented in frequency space because the effects are so clear there. 
  
The problem arises when we make the reasonable suggestion that we should be able to *generate* some simulated <em> time domain </em> data to compare our expectations with what is actually measured. This is fairly straightforward in the case of white noise:

<ol>
  <li> Generate the noiseless data </li>
  <li> Generate an appropriate quantity of random numbers </li>
  <li> Add your random numbers to the noiseless data and voila! </li>
  </ol>
      
  
*Note to self: add some pictures of this process *

Things become a little trickier when the 'power' part of 'power law noise' deviates from zero. In my case, this leads to a lengthy odyssey delving deep into the guts of Fourier transforms. Simply trying to keep track of errant, vanishing, or omitted factors of $$ 2\pi $$ took days of time. What I eventually found was the paper [**On Generating Power Law Noise**](http://adsabs.harvard.edu/full/1995A%26A...300..707T) by J. Timmer and M. Konig.

First, Timmer and Konig point out that there is a way to do this, but it sucks. Not only is it slow as hell, but it is not even fully random. It only randomizes the phase of the data, not the frequency amplitudes!

There is a better way, one that leverages the speed and elegence of the Fast Fourier Transform. Here it is verbatim:
>
<ul>
  <li> Choose power law spectrum $$ S(\omega) ~ (1/\omega)^{\beta} $$.</li>
<li> For each Fourier frequency $$ \omega_i $$, draw two Gaussian distributed random numbers, multiply them by $$ \sqrt{\frac{1}{2} S(\omega)} ~ (1/\omega_i)^{\beta/2} $$ and use the result as the real and imaginary part of the Fourier transform of the desired data. </li>
<li> In the case of an even number of data points, for reasons of symmetry, $$ f(\omega_{Nyquist}) $$ is always real. Thus only one random Gaussian distributed variable has to be drawn. </li>
<li> To obtain a real valued time series, choose the Fourier components for the negative frequencies according to $$ f(-\omega_i) = f^\*(\omega_i) $$ where the asterisk denotes complex conjugation. </li>
<li> Obtain the time series by backward Fourier transformation of $$ f(\omega) $$ from the frequency domain to the time domain. </li>
  </lu>


