# Advanced Concepts
#rnd 

- [ ] nCCM to SSD direct readout without PS involvement. Will need development of NVME controller core.
- [ ] Pipelining image readout and storage. 
- [ ] Payload electronics low-power mode to store time-tagged TC.
- [ ] Aux data directly received at Payload electronics.
- [ ] RTL core for parsing nCCM image data.
- [x] Image data organization: Entire image data as a single file that is sliceable along any axis.
- [x] Merge image database and downlink database
- [ ] CFDP
	- [ ] Combine `metadata_semantic_tlv_list` and `filestore_responses_list` in `txn` structure?
	- [ ] Implement other Message to User features

- [ ] High-speed memory controller
- [ ] Partial reconfiguration 
- [ ] Partial reconfiguration of root file system?
- [ ] Unikernels
- [ ] Foundational Models