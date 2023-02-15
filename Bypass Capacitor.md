# Bypass Capacitor

A bypass [[Capacitor|capacitor]] is used to reduce unwarranted noise at on power supply or at the output of an IC. Typically, the output from a power supply tends to be noisy, or not as clean, due to the coupling with mains (leading to 50 / 60 Hz noise). A bypass capacitor is usually applied between VCC and GND to eliminate the voltage spikes on the power supply and reduce the noise. They are usually applied at two points: 
- At the power supply to eliminate voltage drops by storing charge and releasing it during voltage spikes. It allows only the high frequency signals, which are typically AC noise, to pass through thereby effectively [[Shunt|shunting]] them to the ground and giving a cleaner DC output.
  ![[Pasted image 20230215084142.png]]
- In digital circuits, bypass capacitors act as charge reservoirs. Large currents are required to drive the signal when the logic gates are switching at high frequency. However, the parasitic resistance and inductance impeded the rapid flow of currents. Here, a bypass capacitor placed as close pin as possible reduces parasitic inductance and provides the necessary instantaneous current before the power supply kicks-in.

## Considerations
The following factors must be taken into account while selecting a bypass capacitor:
- Capacitor type: In choosing bypass capacitors, especially for high frequency (switching >100 MHz), it is necessary to minimize the [[Stray Inductance|lead inductance]]. High lead inductances will make the capacitor behave like an open circuit and prevent it from supplying the necessary current.
- Capacitor placement: Capacitors must be placed as close to the pin as possible since any increase in the PCB trace increases the inductance (and resistance) effectively eating into the capacitor's bandwidth and negating the shunting effect.
- Capacitor size: Size is crucial in determining the ability to supply instantaneous current to the circuit. There are two things to be considered:
	- Amount of current required while transitioning from low to high
	- Maximum [[Pulse Slew Rate|pulse slew rate]] 
- Output load effect: Whether the output is purely resistive or capacitive determines the effect of higher frequencies. Capacitive loads lead to high transient currents and oscillations in the supply.

# References: 
[1] https://www.electronicshub.org/bypass-capacitor-tutorial/