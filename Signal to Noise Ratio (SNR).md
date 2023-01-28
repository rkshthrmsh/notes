# Signal to Noise Ratio (SNR)
SNR is an indication of the signal quality in an image. It is defined as the ratio between recorded signal and total root mean squared noise.
$$SNR = \frac{signal}{\Sigma\sqrt{noise^2}}$$
![[Pasted image 20220829172249.png]]

The maximum attainable SNR of a sensor is given by,
$$SNR_{max} \approx \frac{FW}{\sqrt{FW}}$$
This is an approximation based on shot noise, i.e., noise caused by random arrival of photons to a pixel, being much larger than all other noises. This is a valid approximation when strong signals are used and hence lots of light is available. It can also be inferred that sensors with large [[Full Well Capacity|full well capacities]] tend to have larger SNRs. SNR will be less than the dynamic range because of noise in the image. Maximizing SNR is challenging due to lighting conditions, exposure time limitations, and choice of optics.