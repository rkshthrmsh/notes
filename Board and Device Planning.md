## Board and Device Planning
#ultrafastDesign 

Proper board planning covering device orientation and signal assignment to specific pins can lead to significant improvements in system performance, power consumption, thermal performance, and design cycle times.

### Aligning with Physical Components on the PCB
The orientation of the PCB on the device should first be established taking into account the location of fixed PCB components and internal device resources. For example, high-speed components such as GT and memory interfaces should have very short and direct connections. Package pins closes to the edge of the device should be aligned with such components on the PCB.

To ensure better thermal properties, device placement in relation to other high power components needs to be considered carefully. Placement of the device in the exhaust of another high-power component might increasing thermal coupling, restrict airflow, and lead to negative impact on operating temperature.

### Power Distribution System
Unlike other large, dense ICs which come with specific [[Bypass Capacitor|bypass capacitor]] requirements (related to the specific tasks implemented in the hardened silicon), Xilinx devices can be used for a multitude of applications with different frequencies and multi-clock domains. Therefore a careful analysis of power requirements is necessary to design the power distribution system. Key factors to consider include:
- Selecting appropriate [[Voltage Regulator|voltage regulator]]
- Adding shunt resistors, PMBus-enabled regulators, or current monitoring ICs to consolidate power
- Running power distribution simulations. This will help determine the exact number of decoupling capacitors required to guarantee power supplies within the expected range. Xilinx recommends using SIMPLIS simulator in SIMetrix/SIMPLIS (third-party software).