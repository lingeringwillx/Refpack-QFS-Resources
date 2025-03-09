### Headers

Various headers for the RefPack compression algorithm could be found in EA's games:

#### EAC

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

For old 90's titles, alternative algorithms could be used in place of RefPack depending on the flags. This includes Huffman Encoding, Byte-Pair Encoding, and Run-Length Encoding. For more information:

1. [C&C: Generals source code](https://github.com/electronicarts/CnC_Generals_Zero_Hour/tree/main/Generals/Code/Libraries/Source/Compression/EAC)

2. [Martin Korth](https://problemkaputt.de/psxspx-cdrom-file-compression-ea-methods.htm)

3. [Stunts Wiki](https://wiki.stunts.hu/wiki/Compression#EAC_packing)

#### Maxis

In the early to mid 2000's, Maxis deviated from the standard by adding the compressed size before the flags and magic character. Later Maxis games use the regular header.

```
4 bytes int compressed size (little endian)

2 bytes int magic header (0x10FB)

3 bytes int uncompressed size (big endian)
```

#### Notes

- 0xFB likely stands for Frank Barchard, the developer of the algorithm.

- According to EA's source code, support for the `0b10000000` flag was added in 2001[^1]. Older titles are limited to 3 bytes for the uncompressed size in the header, which means that the maximum size of the resources that they can compress is 16 MB.

- The uncompressed size is stored in big endian even in games that natively use little endian.

- I have not seen the `0b00000001` flag in any game files. It's likely never or rarely used. EA's source code shows that the decompressor supports this flag[^2]. However, the compressor doesn't produce any assets with the flag[^3].

- If flag `0b01000000` is set, then the maximum offset is limited to a specific value, exceeding this value might cause the game to crash[^4]. This flag is typically ignored in modder made implementations of the algorithm as it doesn't affect decompression, and when compressing a file it's just left unset.

[^1] [C&C Generals source code: refabout.cpp](https://github.com/electronicarts/CnC_Generals_Zero_Hour/blob/main/Generals/Code/Libraries/Source/Compression/EAC/refabout.cpp)

[^2] [C&C Generals source code: refdecode.cpp](https://github.com/electronicarts/CnC_Generals_Zero_Hour/blob/main/Generals/Code/Libraries/Source/Compression/EAC/refdecode.cpp)

[^3]: [C&C Generals source code: refencode.cpp](https://github.com/electronicarts/CnC_Generals_Zero_Hour/blob/main/Generals/Code/Libraries/Source/Compression/EAC/refencode.cpp)

[^4]: [SimsWiki](https://simswiki.info/wiki.php?title=Sims_3:DBPF/Compression)