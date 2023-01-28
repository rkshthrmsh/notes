# Fabry-Perot Filter
A Fabry-Perot interferometer is a device that uses multiple beam interference of light for high resolution spectroscopy[[Fabry-Perot Filter#^Ref-2|[2]]]. The Fabry-Perot interferometer is useful because the conditions for which the device produces constructive interference are very strict so that its resolution can be very high. It is typically used to resolve sources of light that have narrowly separated wavelengths. It consists of a cavity bounded on each end by a partially-silvered mirror. If the mirrors can be moved in relation to each other the device is called an “interferometer”. If the mirrors are fixed in relation to each other (such as with spacers) then it is called an “Etalon” or a Fabry-Perot filter[[Fabry-Perot Filter|[1]]].

![[Pasted image 20220912213008.png]]
## Operation
Operation is as follows:
* Light is directed onto the outside of one of the mirrors.
* Most is reflected and some enters the cavity.
* When it reaches the opposite mirror some (small proportion) passes out but most is reflected back.
* At the opposite mirror the same process repeats.
* This continues to happen with new light entering the cavity at the same rate as light leaves it.

If you arrange the cavity to be exactly the right size, interference patterns develop which cause unwanted wavelengths to undergo destructive interference. Only one wavelength (or narrow band) passes out and all others are strongly attenuated.

The interesting effects occur on the mirror at entry to the cavity:
* When light of a non-resonant wavelength reaches the mirror most of it (depending on the mirror’s reflectance) will be reflected. The small amount that gets through into the cavity will bounce around for a while but will ultimately exit through one of the end mirrors or be absorbed by losses.
* When light of the resonant wavelength reaches the entry mirror it passes through into the cavity without loss! This is a very interesting effect.
	* Assuming there is already light of the correct wavelength resonating within the cavity a proportion (small) of this light will try to exit from the cavity (because the mirror is only partially reflective).
	* Arriving light that is at the resonant wavelength and coherent with that in the cavity will try to reflect (or most of it will).
	* However, there is destructive interference between light (of the resonant wavelength) leaving the cavity and light coherent with it reflecting from the entry mirror.
	* The result here is that 100% of the incident resonant wavelength light passes through the mirror and into the cavity!
	* In addition light of the resonant wavelength already inside the cavity cannot exit through this mirror (because of the destructive interference with incoming light). 100% of it is reflected and therefore it can only leave the cavity through the opposite mirror! (Or perhaps be absorbed by losses.)  

Thus light of only the resonant wavelength is accepted into the cavity without loss while all other wavelengths suffer significant reflection. The most important requirement for the functioning of a Fabry-Perot filter is that the reflecting surfaces should be extremely flat (preferably within one hundredth of a wavelength) and absolutely parallel. And of course this is the biggest challenge in building them. Typically, the glass surfaces are silvered so that each surface forms a 99% reflective mirror.

## Finesse 
The recognized measure of “goodness” of an FP filter is called the “Finesse”. This is the ratio of the amount of energy stored within the filter to the amount of energy passing through it. It is much the same concept as the “Q” in electrical circuits. The higher the finesse the narrower the passband and the sharper the boundaries. The major factor influencing the finesse is the reflectivity of the mirrors - the more reflective the mirrors the higher the finesse. Absorption within the device and especially within the mirrors reduces the sharpness of the filter peaks.

This is illustrated in Figure 192. Notice that the three lines representing mirror reflectivities of 5%, 70% and 90% respectively all reach 100% transmission (through the filter) at the peak. The peaks are the resonant wavelengths (the lobes of the filter). The distance between the peaks is called the “Free Spectral Range” (FSR) of the FP filter.
$$FSR = \frac{\lambda^2}{2\times n\times D}$$
Here, n equals the RI of the material between the mirrors and D equals the distance between them.
![[Pasted image 20220912213723.png]]
## References
https://imedea.uib-csic.es/~salvador/docencia/coms_optiques/addicional/ibm/ch05/05-23.html ^Ref-1

http://www.yorku.ca/marko/PHYS2212/Lab7 ^Ref-2