### Performance Power Trade-Off for BRAMs
#ultrafastDesign 

While working with memories synthesis attributes can be used to improve the performance / power trade-off between different implementations of the BRAM.
- The `cascade_height` attribute determines the number of cascades of smaller (more primitive) BRAM used in implementing the entire BRAM needed by the design. Increasing the cascade height reduces (improves) power consumption, since only one of the primitive BRAMs will be active at any time. However, additional logic is required to access the correct BRAM which can effect clock frequency.
- On the other hand, setting a cascade height of 1 leads to a parallel BRAM implementation wherein multiple smaller (more primitive) BRAMs are integrated in parallel with each unit supplying a part of the data width. This has the most optimized clock frequency, but inefficient power consumption since all units need to be active at all times.
- A similar result for optimal power can be achieved by using the `ram_decomp` synthesis attribute.