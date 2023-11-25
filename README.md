## Refpack/QFS Resources
Resources for those interested in learning about the Refpack/QFS compression algorithm used in many EA games

### Compression

[Niotso Wiki](http://wiki.niotso.org/RefPack): Generic information on the compression.

[Explanation of The LZ77/LZSS Compression](https://go-compression.github.io/algorithms/lzss/): Refpack/QFS is based on this compression.

[Interactive LZ77 Encoder/Decoder](https://go-compression.github.io/interactive/lz/lz/)

[Explanation of zlib](https://www.euccas.me/zlib/): zlib is a popular compression library and a lot of the techniques employed in zlib are utilized in Refpack/QFS compression code.

[ModTheSims](https://modthesims.info/wiki.php?title=DBPF/Compression): Information about the compression algorithm

[How to Compress](https://github.com/lingeringwillx/CrappySims2Compression/blob/main/how-to-compress.md): I tried to explain the compression algorithm here.

#### Archieve Formats

[FAR](http://wiki.niotso.org/FAR): for The Sims 1

[DBPF 1](https://modthesims.info/wiki.php?title=DBPF): for The Sims Online, SimCity 4, and The Sims 2

[DBPF 2](https://modthesims.info/wiki.php?title=Sims_3:DBPF): for The Sims 3

#### Implementations

List of known implementations of the compression algorithm organized by language and [compression header]() for easy reference. A lot of those are rewrites of the same code in another programming language. The quality varies from one implementation to another.

| Language | Header 1 | Header 2 | Header 3 |
|-|:-:|:-:|:-:|
| C | [Denis Auroux](https://math.mit.edu/~auroux/software/fshtool.zip) | [benrg](http://www.moreawesomethanyou.com/smf/index.php/topic,8279.0.html) ||
| C# | [0xC0000054](https://github.com/0xC0000054/DBPFSharp/blob/main/src/DBPFSharp/QfsCompression.cs)<br>[gibbed](https://github.com/gibbed/Gibbed.RefPack) | [0xC0000054](https://github.com/0xC0000054/DBPFSharp/blob/main/src/DBPFSharp/QfsCompression.cs)<br>[Afr0](https://github.com/riperiperi/FreeSO/blob/master/Other/tools/SimsLib/SimsLib/FAR3/Decompresser.cs)<br>[ambertation](https://github.com/luki122/simpe/blob/master/fullsimpe/SimPe%20Packages/PackedFile.cs) | [pljones & Tiger](https://sourceforge.net/p/s3pi/git/ci/master/tree/s3pi/Package/Compression.cs) |
| C++ | [KUDr](https://github.com/MicaelJarniac/RefPack-Tool) | [lingeringwillx](https://github.com/lingeringwillx/CrappySims2Compression/tree/main/practice) | [J.M. Pescado](https://gist.github.com/uyjulian/bd24b98a4c97b775c9ab) |
| Java || [java_dwarf](https://github.com/memo33/jDBPFX/blob/master/src/jdbpfx/util/DBPFPackager.java) ||
| JavaScript || [sebamarynissen](https://github.com/sebamarynissen/qfs-compression) ||
| PHP || [Delphy](https://modthesims.info/wiki.php?title=DBPF_Compression#Example_Code)* ||
| Python || [lah7](https://github.com/lah7/sims2-4k-ui-mod/blob/master/qfs.py)<br>[lingeringwillx](https://github.com/lingeringwillx/sims2lib/blob/main/dbpf.py)** ||
| Rust || [actioninja](https://github.com/actioninja/refpack-rs) | [actioninja](https://github.com/actioninja/refpack-rs) |
| Scala || [memo33](https://github.com/memo33/scdbpf/blob/master/src/main/scala/scdbpf/internal/QfsCompression.scala) ||

*Decompression code only

**C Bindings

[C++ Reference Implementation by EA](http://download.wcnews.com/files/documents/sourcecode/shadowforce/transfer/asommers/mfcapp_src/engine/compress/RefPack.cpp)
