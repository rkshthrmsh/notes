# Chisel
[Chisel](https://www.chisel-lang.org/) stands for Constructing hardware in a Scala Embedded Language. It is an open-source HDL used to describe digital electronics and circuits at the RTL level. Chisel facilitates advanced circuit generation and design reuse for both ASIC and FPGA digital logic designs. Chisel provides hardware designers new programming paradigms that allow them to be more productive: 
1. Object oriented programming
2. Functional programming
3. Libraries

Chisel forms part of a Hardware Compiler Framework wherein the Chisel-to-Verilog process is a multi-stage process. Hardware generators written in Chisel get compiled to a circuit intermediate representation (IR) called [FIRRTL](https://github.com/chipsalliance/firrtl) using a front-end. This IR can then be optimized using custom user transformations. Finally a back-end emits Verilog based on the optimized FIRRTL. This multi-stage compiler structure has the following benefits:
1. Decoupling the front-end and back-end enables development of other front-end and back-end stages. Examples include:
	1. [Yosys](https://yosyshq.net/yosys/): A framework for Verilog RTL synthesis which provides a basic set of algorithms for various application domains.
	2. Magma is a Domain Specific Language in Python that can target FIRRTL.
2. Having a circuit IR allows automated specialization/ transformation of circuits. Examples include:
	1. [[Chiffre]] enables easy, scalable experimentation with run-time configurable fault injection. #on-Zotero 
	2. [DESSERT](https://carrv.github.io/2018/papers/CARRV_2018_paper_10.pdf), an FPGA-accelerated methodology for simulation-based RTL verification. #on-Zotero
	3. [Midas](https://carrv.github.io/2017/papers/kim-midas-carrv2017.pdf), an open-source, performance, power, and energy evaluation methodology using RTL designs by running realistic workloads to completion in FPGA simulation.
	4. [FireSim](https://sagark.org/assets/pubs/firesim-isca2018.pdf), a simulation platform for warehouse-scale architectural research using public FPGA cloud platforms.

# References
https://stackoverflow.com/questions/53007782/what-benefits-does-chisel-offer-over-classic-hardware-description-languages
