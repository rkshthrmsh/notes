# CMOS Sensor
#sensor
Complementary Metal Oxide Semiconductor sensors contain photodiodes coupled with individual amplifiers to to capture photons, convert them to electrons, and amplify the electrical signal.

## Structure
A PN junction photodiode is at the heart of most CMOS photo-detectors. When a a photodiode is reverse biased, a current proportional to the incident light intensity will flow through the diode. Since this current increases linearly with light intensity, it can be used to replicate a scene. The following is an abstract representation of the photodiode along with the linear time-dependent  charge accumulation.

![[Pasted image 20221003213017.png]]

![[c003s005f001.gif]]



## Advantages
CMOS sensors broke into the scene as an alternative to [[Charge-Coupled Device (CCD)|CCD]] and have proliferated the market spurred by their demand in mobile digital imaging. Some advantages of CMOS sensors are:
* In CMOS, the front end of the data path is massively parallel which allows each amplifier to have low bandwidth. By the time the signal reaches the interface between the imager and off-chip circuitry, CMOS data is already digitized.

![[Pasted image 20221003213633.png]]

* CMOS sensors are fabricated in standard CMOS technology. This provides a significant advantage since the sensor along with the readout circuitry can be fabricated together thereby making the imaging devices compact and highly integrated. On the other hand CCD require special-purpose fabrication technologies.
* The integrated solution reduces power consumption

## Disadvantages
Due to the highly integrated nature of CMOS sensors, there are multiple active devices in the readout path that can produce time-varying noise. Further, fabrication inconsistencies could lead to mismatch in charge-to-voltage amplifiers of different pixels leading to fixed [[Pattern Noise|pattern noise]].

### Rolling Shutter Artifacts
In many highly integrated CMOS sensors with a large number of pixels, the exposure cycle of different pixels rows starts at slightly different times. The structure of the device leads to light integration happening in sequence from top to the bottom which leads to a distortion called rolling shutter artifact when capturing a moving object. With a fast-moving object the scene can change by the time all of the pixels are captured.

Modern sensors have much higher readout rates; global shutter, reset, exposure cycle for all pixels to avoid such an artifact.

## References
https://www.allaboutcircuits.com/technical-articles/introduction-to-cmos-image-sensors/

https://www.teledynedalsa.com/en/learn/knowledge-center/ccd-vs-cmos/

https://global.canon/en/technology/s_labo/light/003/05.html