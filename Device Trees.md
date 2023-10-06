---
tags:
  - embeddedLinux
---
### Device Trees
A device tree is a flexible way of defining the hardware components of a system. It represents the system as a collection of components joined together in a hierarchy, starting with a root nod (`/`) and all other subsequent nodes.
```
/dts-v1/;
/{
	model = "TI AM335x BeagleBone";
	compatible = "ti,am33xx";
	#address-cells = <1>;
	#size-cells = <1>;
	cpus {
		#address-cells = <1>
		#size-cells = <0>;
		cpu@0 { 
			compatible = "arm,cortex-a8";
			device_type = "cpu";
			reg = <0>;
		}; 
	};
	memory@0x80000000 { 
		device_type = "memory";
		reg = <0x80000000 0x20000000>; /* 512 MB */
	};
};
```

In the example above, the device tree contains a `cpus` node and a `memory` node. The names of the leaf nodes are often followed by an `@` and an address that distinguishes it from other nodes of the same type.
#### Properties
- The `compatible` property is used by the kernel to find a matching device driver by comparing it with the strings that are exported by each device driver in the `of_device_id` structure. By convention, this property includes the manufacturer name and component name to reduce confusion. It is also common to have more than one value for the compatible property when there is more than one driver capable of handling this device.
- The `device_type` property describes the class of device.
- The `reg` property refers to a range of units in register space. It consists of two values, the real physical address and the size of the range. These values are written as zero or more 32-bit integers called *cells*. For example, two cells would be required to represent the address on a device with 64-bit addressing.
- The number of *cells* required to represent a value is held in the `#address-cells` and `#size-cells` properties in an ancestor node.
- The `phandle` property acts as a label for a node to enable a device tree to express a more complicated hierarchy of devices. The label can be reused anywhere in the device tree to refer to the particular node.
#### Device Tree Include Files
Device tree include files, with the `.dtsi` extension, can be used to split the common hardware between SoCs. These files can then by added to the `.dts` using and `include` statement. When included, the nodes are overlaid on top of each other to create a composite tree in which the outer layers extend or modify the inner ones. The label of the node is used to extend or modify the node.

#### Compiling the Device Tree
Finally, the bootloader and the kernel require a binary representation of the device tree (`*.dtb`) which is obtained by compiling the `.dts` file. `dtc` is available as a script or a package on Linux distributions for this purpose. However, compiling complex real-world examples requires the Kbuild kernel.