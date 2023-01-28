# Indium Gallium Arsenide Sensors
#sensor 
InGaAs sensors are used when sensitivity in the 900--1700 nm wavelength range is required[[Indium Gallium Arsenide Sensors#^Ref-1|[1]]]. In some designs the range can be extended up to 2500 nm based on changes in material composition. Although silicon based [[Charge-Coupled Device (CCD)|CCD]] cameras have excellent sensitivity over the UV-to-NIR range, the band gap properties of silicon prevent the CCDs from having sufficient sensitivity over 1100 nm. InGaAs sensors on the other hand have lower [[Band Gap#Band Gap|band gap]] making them preferable for applications in [[Short Wave Infrared (SWIR)#Short Wave Infrared SWIR|SWIR]]. 

![[Pasted image 20220912160733.png]]

InGaAs is a III-V compound semiconductor whose doping concentration can be tailored to meet specific application requirements.

## InGaAs FPA
A typical InGaAs [[Focal Plan Array|FPA]] consists of a 2-D photodiode array -- comprising an indium phosphide (InP) substrate, an InGaAs absorption layer, and an ultrathin InP cap -- that has been indium bump bonded to a ROIC.

![[Pasted image 20220912163451.png]]

One noteworthy behaviors of InGaAs FPAs is that their long-wavelength cutoff is reduced when they are cooled -- approximately, the long-wavelength cutoff shifts inwards by 8nm for every 10$\degree$C of sensor cooling. This can make the sensor behave like a tunable lowpass optical filter.

## Limitations
The biggest limitation of InGaAs cameras is their noise level[[Indium Gallium Arsenide Sensors#^Ref-2|[2]]]. Due to the low bandgap of InGaAs material, InGaAs FPA cameras have much higher [[Dark Current Noise|dark current]] than Si-CCD cameras. Therefore, it is absolutely critical to minimize InGaAs FPA cameras’ dark noise via deep cooling. Another limitation is pixel defects. Due to the intricacy involved in sensor fabrication defective pixels are inevitable. However, these can be compensated for suing defect-correction algorithms. Lastly, fixed [[Pattern Noise|pattern noise]] is also an issue caused by gain or response variations.

## InGaAs FPA vs Si-CCD Cameras
![[Pasted image 20220912160852.png]]


# References
https://www.princetoninstruments.com/learn/swir-nirii/intro-to-scientific-ingaas-fpa-cameras ^Ref-1

https://www.princetoninstruments.com/learn/camera-fundamentals/ingaas-sensors-the-basics ^Ref-2