# CCSDS Compression
#rnd
- [x] Pack codewords tightly into bytes to avoid losing compression efficiency.
- [ ] Pack metadata as header along with compressed pixels. 
      The header contains fields that can have sub-byte lengths, and moreover, the number of header fields can vary—this affects the header length. Since bit manipulation in Rust is non-trivial and handling of variable-length headers can be tricky, more time is needed to figure it out. 
      Helpful links:
      - https://github.com/bincode-org/bincode
      - https://docs.rs/bitvec/latest/bitvec/vec/struct.BitVec.html#method.set: "_If you wish to use bit-field memory representations as `struct` fields rather than a transport buffer, consider `BitArray` instead: that type keeps its data as an immediate, and is more likely to act like a C struct with bitfields._"
    
	In the meantime, use a separate file (.json) to pack metadata.
	
	- [x] Both Predictor and Encoder have three separate structs: _Config + Struct + Metadata_. This can be hard to maintain. Is it possible to reduce this?
	      This is not necessary at the moment. Instead, _Config_ will act as the entry point for both _Struct_ and _Metadata_.
		- [x] Can _Config_ be subsumed under _Struct_?
		      This is not possible because the _Structs_ contain derived fields. Subsuming _Config_ would require a way to update the _Struct_ whenever the fields are updated—which makes for an awkward API.
		- [x] Implement `From<Config>` trait for _Structs_.
	- [x] Pack _Config_ as metadata into a separate file (.json)
- [x] Update `EncodeDecodeMethods` trait to accept `BufWriter` / `BufReader` handle directly and read from them
	- [x] Update `decode` method
		- [x] Output of `decode` method is expected to be `u32`. There are potentially multiple ways of achieving this:
		- ~~Create a `BitVec` to accept bytes from `BufReader`, `drain` a full codeword from it, and then convert it into `usize` using `from_be_bytes`. Since codeword can be of variable length, the incomplete byte will need padding with MSB `0`s. Moreover, even the number of bytes needs to be extended to 8 for `usize::from_be_bytes`.~~
		- Create a `BitVec` to accept bytes from `BufReader`, and `drain` a full codeword into a `BitArr<[usize; 1], Msb0>` which can be converted into `usize`
	- [x] Update `encode` method
	      Updated `encode` to use `bitvec` instead of `String`. Seeing an improvement in compression speeds from 500+ s to 300+ s.
- [x] Update integration test to `assert_eq!(uncompressed, decompressed)` for a small image
- [x] Check decompression speeds
      Where compression takes ~430s, decompression takes ~320s. Could the speed be due to `bitvec`?
- [x] Create `help` mode
- [x] Add ability to read from `.tif` file.
      Used `gdal` crate to achieve this. 
- [ ] Integrate compression / decompression with `.tif` file structure
- [ ] Accelerate compression through parallelization by splitting image into blocks.
	- [ ] Test with geometric blocks, i.e., dividing image into equal cubes / cuboids
	- [ ] Test with feature-based blocks, i.e., dividing image as per features such as water bodies, green cover, clouds, etc.