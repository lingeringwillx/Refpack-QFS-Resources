## Refpack/QFS Resources

Resources for those interested in the Refpack/QFS compression algorithm used in many EA games. Also maintains links to known implementations of the compression for easy reference.

#### About

Refpack/QFS is a compression algorithm based on the LZ77/LZSS compression written by Frank Barchard for use in games made by EA. It can be found in games as early as FIFA International Soccer (1993). Refpack/QFS has a larger sliding windows compared to other old compression algorithms, as well as a larger maximum match length.

The longer length and offset capacity of the compression slows down the compression, and it doesn't seem to yield a significant improvement in the compressed size, so I don't know why EA didn't just use DEFLATE. It's possible that EA waited until the patent for the DEFLATE algorithm expired before it started using it in its own games.

#### Resources

[Niotso Wiki](http://wiki.niotso.org/RefPack): Generic information on the compression.

[Explanation of The LZ77/LZSS Compression](https://go-compression.github.io/algorithms/lzss/): Refpack/QFS is based on this compression.

[Interactive LZ77 Encoder/Decoder](https://go-compression.github.io/interactive/lz/lz/)

[Explanation of zlib](https://www.euccas.me/zlib/): zlib is a popular compression library and a lot of the techniques employed in zlib are utilized in Refpack/QFS compression code.

[How to Compress](https://github.com/lingeringwillx/Refpack-QFS-Resources/blob/main/how-to-compress.md): I try to explain the compression algorithm here.

[ModTheSims](https://modthesims.info/wiki.php?title=DBPF/Compression): Information about the compression algorithm. Has some mistakes.

#### Implementations

List of known implementations of the compression algorithm organized by language and [compression header](https://github.com/lingeringwillx/Refpack-QFS-Resources/blob/main/how-to-compress.md#compression-header) for easy reference. A lot of those are rewrites of the same code in another programming language. The quality varies from one implementation to another.

| Language | Header 1 | Header 2 | Header 3 |
|-|:-:|:-:|:-:|
| C | [Denis Auroux](https://math.mit.edu/~auroux/software/fshtool.zip) |||
| C# | [0xC0000054](https://github.com/0xC0000054/DBPFSharp/blob/main/src/DBPFSharp/QfsCompression.cs) | [0xC0000054](https://github.com/0xC0000054/DBPFSharp/blob/main/src/DBPFSharp/QfsCompression.cs)<br>[Afr0](https://github.com/riperiperi/FreeSO/blob/master/Other/tools/SimsLib/SimsLib/FAR3/Decompresser.cs)<br>[ambertation](https://github.com/luki122/simpe/blob/master/fullsimpe/SimPe%20Packages/PackedFile.cs) | [pljones & Tiger](https://sourceforge.net/p/s3pi/git/ci/master/tree/s3pi/Package/Compression.cs)<br>[gibbed](https://github.com/gibbed/Gibbed.RefPack) |
| C++ || [benrg](http://www.moreawesomethanyou.com/smf/index.php/topic,8279.0.html)<br>[lingeringwillx](https://github.com/lingeringwillx/CrappySims2Compression/tree/main/practice) | [OmniBlade](https://github.com/TheAssemblyArmada/Thyme/blob/develop/src/game/common/compression/refpack.cpp)<br>[KUDr](https://github.com/MicaelJarniac/RefPack-Tool)<br>[J.M. Pescado](https://gist.github.com/uyjulian/bd24b98a4c97b775c9ab) |
| Go | [marcboudreau](https://github.com/marcboudreau/godbpf/blob/master/qfs/qfs.go) |||
| Java || [java_dwarf](https://github.com/memo33/jDBPFX/blob/master/src/jdbpfx/util/DBPFPackager.java) ||
| JavaScript | [sebamarynissen](https://github.com/sebamarynissen/qfs-compression) | [sebamarynissen](https://github.com/sebamarynissen/qfs-compression) ||
| Kotlin | [DarkAtra](https://github.com/DarkAtra/bfme2-modding-utils/blob/main/refpack/src/main/kotlin/de/darkatra/bfme2/refpack/RefPackInputStream.kt)* |||
| PHP || [Delphy](https://modthesims.info/wiki.php?title=DBPF_Compression#Example_Code)* ||
| Python || [lah7](https://github.com/lah7/sims2-4k-ui-mod/blob/master/qfs.py)<br>[lingeringwillx](https://github.com/lingeringwillx/sims2lib/blob/main/dbpf.py)** ||
| Rust || [actioninja](https://github.com/actioninja/refpack-rs) | [actioninja](https://github.com/actioninja/refpack-rs) |
| Scala || [memo33](https://github.com/memo33/scdbpf/blob/master/src/main/scala/scdbpf/internal/QfsCompression.scala) ||

*Decompression code only

**C Bindings

[Reference Implementation by EA](http://download.wcnews.com/files/documents/sourcecode/shadowforce/transfer/asommers/mfcapp_src/engine/compress/RefPack.cpp)
