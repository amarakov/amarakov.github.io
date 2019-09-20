---
layout: post
title: On Generating Power Law Noise
description: Some thoughts details about J. Timmer and M. Konig's paper- On Generating Power Law Noise
---

I work in superconducting electronics. This almost always means Josephson junctions and SQUIDs are involved, and it also means that magnetic flux noise is the bane of my existence. Indeed, I gave a short talk to this end at a conference (ISEC 2019). Those of you who deal with qubits other than the venerable transmon, are likely battling charge noise AND flux noise. I don't mean to suggest that superconducting electronics as a field has a monopoly on noise (we don't), but it is my primary focus. Needless to say, pretty much everyone with a quantitative job runs in these problems.

SQUIDs in particular exhibit something that is colloquially called 'pink noise' (as opposed to familiar 'white noise' or the less commonly used 'red noise'). I think these names are dumb and confusing, because these are all manifestations of a single type: Power Law Noise.

$$ S(\omega) = A^2/f^{\alpha}  $$

Pink noise, has a power $$ \alpha = 1 $$, white noise has power $$ \alpha = 0 $$ and 'red noise' has power $$ \alpha = 2 $$. We also call pink noise '1/f noise'.

A standard way of characterizing SQUID loops for sensing applications (magnetic fields usually, but that's not all they're good for!) is by measuring the noise power spectral density of the device. That is to say, measuring how much noise the SQUID sees in each decade of frequency. It's important to note that this is <em> always </em> represented in frequency space because the effects are so clear there.

The problem arises when we make the reasonable suggestion that we should be able to *generate* some simulated <em> time domain </em> data to compare our expectations with what is actually measured. This is fairly straightforward in the case of white noise:

<ol>
  <li> Generate the noiseless data </li>
  <li> Generate an appropriate quantity of random numbers </li>
  <li> Add your random numbers to the noiseless data and voila! </li>
  </ol>



Things become a little trickier when the 'power' part of 'power law noise' deviates from zero. In my case, this leads to a lengthy odyssey delving deep into the guts of Fourier transforms. Simply trying to keep track of errant, vanishing, or omitted factors of $$ 2\pi $$ took days of time. What I eventually found was the paper [**On Generating Power Law Noise**](http://adsabs.harvard.edu/full/1995A%26A...300..707T) by J. Timmer and M. Konig.

First, Timmer and Konig point out that there is a way to do this, but it sucks. Not only is it slow as hell, but it is not even fully random. It only randomizes the phase of the data, not the frequency amplitudes!

There is a better way, one that leverages the speed and elegance of the Fast Fourier Transform. Here it is verbatim:

<ul>
  <li> Choose power law spectrum \(S(\omega) \sim (1/\omega)^{\beta}\).</li>
<li> For each Fourier frequency \(\omega_i\), draw two Gaussian distributed random numbers, multiply them by \( \sqrt{\frac{1}{2} S(\omega)} \sim (1/\omega_i)^{\beta/2} \) and use the result as the real and imaginary part of the Fourier transform of the desired data. </li>
<li> In the case of an even number of data points, for reasons of symmetry, \(f(\omega_{Nyquist})\) is always real. Thus only one random Gaussian distributed variable has to be drawn. </li>
<li> To obtain a real valued time series, choose the Fourier components for the negative frequencies according to \(f(-\omega_i) = f^{*}(\omega_i)\) where the asterisk denotes complex conjugation. </li>
<li> Obtain the time series by backward Fourier transformation of \(f(\omega)\) from the frequency domain to the time domain. </li>
</ul>

I break up this process into a few steps. First, a quick function to define
the power spectrum:


{% highlight py %}
def S(A,f,a):
    w=2*pi*f
    return np.power(np.power(A,2)/w,a)
{% endhighlight %}

Here I differ in an important way from Timmer and Konig, as their paper does not trouble itself with the details of the power law functional form. You'll note that everything is written in terms of proportionality, rather than equality. This is fine if you're just looking to figure out the power law for your noise, but in cases where you need to know the noise amplitude ($$A$$ in the function above) you'll have to make some modifications to the procedure. There is more on this below.

Next we need to figure out how many frequencies we need to use! This is set by the sampling rate of the simulated data. In a lot of Fourier analysis, there is an implicit assumption that the sampling rate is 1 Hz, which leads to tricky details being swept under the rug!

{% highlight py %}
numPts = int(np.power(2,10))
time = np.linspace(Ts,numPts*Ts,numPts)
freqs = (Fs/2.0)*np.linspace(0,1,numPts/2+1)
{%endhighlight%}

You will have to chose a sampling rate, $$F_s$$, by defining how your data points are spaced in time ($$\Delta t$$), then calculating the inverse. As you can see, the max frequency we use is $$F_s/2$$, which is called the <em> Nyquist frequency </em>. The reason for the factor of two is aliasing from the Fourier transform. Also, I usually choose <code> numPts </code> to be a power of two so that we can leverage the power of the Fast Fourier Transform algorithm.

Now on to Step 3: generating the Fourier components for each frequency.

{% highlight py %}
fourierDomainPos = np.zeros((len(freqs),1), dtype = complex)
for i,f in enumerate(freqs):
    if f==0:
        # Choose DC offset
        fourierDomainPos[i]=0
    elif f == freqs[-1]:
        # Nyquist Frequency gets a real value
        real = np.random.normal()*np.sqrt(0.5*numPts)*S(amp,f,a/2.0)
        fourierDomainPos[i]
    else:
        # Generate random Re, Im pairs for all other freqs
        real = np.random.normal()*np.sqrt(0.5*numPts)*S(amp,f,a/2.0)
        im = np.random.normal()*np.sqrt(0.5*numPts)*S(amp,f,a/2.0)
        fourierDomainPos[i]=real+im*1j


fourierDomainNeg = np.conj(np.flipud(fourierDomainPos[1:-1]))

fourierDomain = np.append(fourierDomainPos, fourierDomainNeg)
{%endhighlight%}

Note that I've added a factor of $$ \sqrt{N} $$ to my Real and Imaginary pair generation. This is not entirely correct. I am implicitly using $$\Delta t = 1s $$ here, the full expression should have a Gaussian random multiplied by $$ \sqrt{\frac{1}{2}N \Delta t S(\omega)} $$.

It is easy to see why this should be the case if we recall the definition of noise power spectral density: $$ S(\omega) = \frac{1}{N\Delta t}X(\omega)X^{*}(\omega)$$, where $$ X(\omega) $$ is the Fourier transform of the time domain data, $$x(t)$$. Since the procedure outlined by Timmer and Konig is generating the Fourier domain data in the form $$X(\omega) \sim \sqrt{\frac{1}{2} N \Delta t S(\omega)}(1+i)$$, we can recover $$S(\omega)$$ by plugging our expression for $$X(\omega)$$ into the power spectral density equation above. Try it and see!

One last point: you will be generating data for positive and negative frequencies. The <code>numpy.fft</code> functions expect the zero frequency point to be element [0] of your array, followed by positive frequency data up to the Nyquist frequency, followed by negative frequency data. The code above follows this convention and will create an ndarray that is ready to be IFFT'd to produce time domain data. Like so:

{% highlight py %}
timeDomainNoise = np.fft.ifft(fourierDomain)
{% endhighlight %}
