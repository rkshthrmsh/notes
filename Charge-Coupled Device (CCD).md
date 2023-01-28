# Charge-Coupled Device
#sensor
A CCD is a highly sensitive photon detector based on MOS capacitors. A CCD is composed of smaller light-sensitive areas known as pixels which build up an image of the scene. When a photon of light falls on the pixel it will be converted into one or more electrons. The number of electrons determines the intensity of the scene at each pixel. When the CCD is "clocked-out", the number of electrons at each pixel is determined to reconstruct the scene.

## Charge Collection
A section of a CCD array consists of rows of electrodes to control charge collection over a buried n-type channel in a p-type substrate. When a photon strikes the CCD, it generates electrons which are collected under positively charged electrodes. The electrodes are made of polysilicon as this is reasonably transparent to the wavelengths of light that the CCD intends to image.

![[Pasted image 20220928163736.png]]

![[Pasted image 20220928164327.png]]

## Readout
The electrodes held in each pixel are arranged to transfer charge downwards along the columns to a final row which is a shift register. The shift register is used to transfer the charge out of the CCD for measurement or amplification. During the readout operation, electrodes are alternatively biased with high and low voltages to transfer the charge.

![[Pasted image 20220928164735.png]]

![[Pasted image 20220928164754.png]]

## Quantum Efficiency
Modern CCDs can achieve QEs of over 90%. Although [[Quantum Efficiency|QEs]] vary across wavelengths, innovations such as back-thinning, back-illumination, anti-reflective coatings, and high resistivity silicon can be used to improve QEs across an extended range of wavelengths.

## Wavelength Range
CCDs cover a wide range of wavelengths which includes 0.1nmto 1000 nm. 

## Dynamic Range
The [[Dynamic Range]] of a CCD is determined in terms of the minimum and maximum number of electrons that can be imaged. The minimum signal that can be detected is not one electron, but rather depends on the amount of [[Readout Noise|readout noise]] associated with the structure of the CCD--usually around 2 to 4 electrons per pixel.

## Linearity
CCDs exhibit good [[Linearity|linearity]]

## Noise 
Noise sources in CCD cameras add in quadrature. In the low-light regime, [[Dark Current Noise|dark current]] noise and [[Readout Noise|readout noise]] are the most significant sources of noise.
$$Total Noise = \sqrt{(read\;noise^2+dark\;noise^2)}$$

### Dark Current
In CCDs at room temperature, the noise performance can be thousands of electrons per pixel per second. [[Dark Current Noise|Dark current]] can be reduced by cooling the detector leading to improved noise performance of tens of electrons per pixel per second at -40° C.

### Readout Noise
The [[Readout Noise|readout noise]] originates form the conversion of electrons in each pixel to a voltage. Reducing CCD readout noise is an important part of current and future CCD development.


## References
https://www.teledyneimaging.com/media/1300/2020-01-22_e2v_how-a-charge-coupled-device-works_web.pdf