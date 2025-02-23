# R&D – AXI DMA Driver
#rnd
- [ ] Coherent DMA mapping or streaming mapping
      Coherent mapping: 
      - Used for long-lasting bi-direction I/O buffers
      - Device and CPU have consistent view of memory
      Streaming mapping:
      - Buffer is mapped right before the transmission and is required to be unmapped right afterwards.
      - If the same streaming region is to be used multiple times, the buffer must be synced properly to ensure that the CPU and the device see the most up-to-date and correct copy of the DMA buffer. This can be done with `dma_sync_{single/sg}_for_{device/cpu}`
  - [ ] If using coherent, ensure AXI DMA IP is synthesized accordingly
- [x] Finish first pass of DMA controller
      - Remove unnecessary code from `xilinx_dma.c`
      - Understand the entire code
- [ ] Implement client DMA driver
      ~~Integrate client driver into the DMA controller~~
	  Went ahead with Xilinx DMA controller instead, and implemented a separate DMA client driver. DMA client driver is implemented as a character device driver. However, the exact method of requesting the DMA channel from the PL AXI DMA IP core is tricky. There maybe two ways of doing this, both worth exploring:
	- [ ] Modify device-tree to add `pixxel-dma-client` node with `dmas` and `dma-names` properties. `dmas` should contain the channel information for the PL AXI DMA IP. Then, this new `platform_device` node can be `probe`d, and a separate character device (`cdev` and `device`) structure be associated with it for exposing `read` and `write` operations.
	  - [ ] Explore whether `dma_request_chan` function's filter can be used to isolate the required channel.
- [ ] Rewrite entire DMA controller
      - Use `regmap`
      - Optimize DMA and channel structs