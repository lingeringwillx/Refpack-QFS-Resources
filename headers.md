## Compression Headers

Various headers for the RefPack compression algorithm could be found in EA's games:

#### Header 1 (90s Games)

```
1 byte flags:

bit 0b00010000: Always set

bit 0b00000001: compressed size added to header

1 byte magic: 0xFB

if flag 0b00000001 set: 3 bytes int compressed size (big endian)

3 bytes int uncompressed size (big endian)
```

Alternative algorithms could be used in place of Refpack depending on the flags. Such as Huffman Encoding, Byte-Pair Encoding, and Run-Length Encoding. For more information:

1- [Martin Korth](https://problemkaputt.de/psxspx-cdrom-file-compression-ea-methods.htm)

2- [Stunts Wiki](https://wiki.stunts.hu/wiki/Compression#EAC_packing)

#### Header 2 (Early 2000s Games)

Found in: The Sims Online, SimCity 4, and The Sims 2

```
4 bytes int compressed size (little endian)

2 bytes int magic header (0x10FB)

3 bytes int uncompressed size (big endian)
```

#### Header 3 (Late 2000s Games - Now)

Found in: Spore, The Sims 3, and The Sims 4

```
1 byte flags:

bit 0b10000000: 4 bytes are used for the decompressed size if enabled, 3 bytes are used if disabled

bit 0b01000000: Restricted sliding window size (Note: Need to double check this)

bit 0b00010000: Always set

bit 0b00000001: compressed size added to header

1 byte magic: 0xFB

if flags 0b10000000 and 0b00000001 set: 4 bytes int compressed size (big endian)

else if flag 0b00000001 set: 3 bytes int compressed size (big endian)

if flag 0b10000000 set: 4 bytes uncompressed size (big endian)

else: 3 bytes uncompressed size (big endian)
```

#### Notes

1- 0xFB likely stands for Frank Barchard, the developer of the algorithm.

2- In older headers, the uncompressed size is stored in a 3 bytes integer. The compression algorithm is pretty old so it's likely that they assumed that 3 bytes is enough for storing the uncompressed size.

3- The uncompressed size is stored in big endian even in games that natively use little endian. It's possible that the algorithm's standard specifies that it should be like this.

4- I have not seen the `0b00000001` flag in any game files. It's likely never or rarely used. However, reverse engineering efforts did show that the algorithm supports this flag.

5- If flag `0b01000000` is set, then the maximum offset is limited to a specific value, exceeding this value might cause the game to crash. This flag is typically ignored in modder made implementations of the algorithm as it doesn't affect decompression, and when compressing a file it's just left unset.