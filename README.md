## Refpack/QFS Resources

Resources for those interested in the Refpack/QFS compression algorithm used in many EA games. Also maintains links to known implementations of the compression for easy reference.

#### About

RefPack is a compression algorithm based on the LZ77/LZSS compression written by Frank Barchard for use in games made by EA[^1]. It can be found in games as early as FIFA International Soccer (1993)[^2]. The algorithm uses different encoding schemes based on the length and position of the data that's being compressed, which allows it to achieve a higher compression ratio compared to other LZ-based algorithms[^1].

The name QFS comes from a file format used in old Need for Speed games in which this algorithm was used. However, the actual name of the algorithm is RefPack[^1].

#### Resources

[Niotso Wiki](http://wiki.niotso.org/RefPack): Generic information on the compression algorithm.

[Source Code](https://github.com/electronicarts/CnC_Generals_Zero_Hour/blob/main/Generals/Code/Libraries/Source/Compression/EAC/refabout.cpp): Official source code released by EA as part of the public release of Command and Conquers: Generals - Zero Hour source code.

[Explanation of LZ77/LZSS Compression](https://go-compression.github.io/algorithms/lzss/): Refpack is based on the LZ77 compression algorithm.

[Explanation of zlib](https://www.euccas.me/zlib/): zlib is a popular compression library and a lot of the techniques employed in zlib are utilized in Refpack compression code.

#### Implementations

List of known implementations of the compression algorithm organized by language and [compression header](https://github.com/lingeringwillx/Refpack-QFS-Resources/blob/main/headers.md) for easy reference. A lot of those are rewrites of the same code in another programming language. The quality varies from one implementation to another.

| Language/Header | EAC | Maxis | EAC (w/ 32-bit size) |
|-|:-:|:-:|:-:|
| C | [Denis Auroux](https://math.mit.edu/~auroux/software/fshtool.zip) |||
| C# | [0xC0000054](https://github.com/0xC0000054/DBPFSharp/blob/main/src/DBPFSharp/QfsCompression.cs)<br>[Venomalia](https://github.com/Venomalia/AuroraLib.Compression/blob/main/src/AuroraLib.Compression/Algorithms/RefPack.cs)<br>[rivit](https://github.com/Killeroo/QFS.net/blob/master/QFS_rivit.cs) | [0xC0000054](https://github.com/0xC0000054/DBPFSharp/blob/main/src/DBPFSharp/QfsCompression.cs)<br>[Venomalia](https://github.com/Venomalia/AuroraLib.Compression/blob/main/src/AuroraLib.Compression/Algorithms/RefPack.cs)<br>[Afr0](https://github.com/riperiperi/FreeSO/blob/master/Other/tools/SimsLib/SimsLib/FAR3/Decompresser.cs)<br>[ambertation](https://github.com/luki122/simpe/blob/master/fullsimpe/SimPe%20Packages/PackedFile.cs) | [Venomalia](https://github.com/Venomalia/AuroraLib.Compression/blob/main/src/AuroraLib.Compression/Algorithms/RefPack.cs)<br>[GlitcherOG](https://github.com/GlitcherOG/SSX-Collection-Multitool/blob/main/FileHandlers/RefpackHandler.cs)<br>[pljones & Tiger](https://sourceforge.net/p/s3pi/git/ci/master/tree/s3pi/Package/Compression.cs)<br>[gibbed](https://github.com/gibbed/Gibbed.RefPack) |
| C++ || [benrg](http://www.moreawesomethanyou.com/smf/index.php/topic,8279.0.html) | [EA Games](https://github.com/electronicarts/CnC_Generals_Zero_Hour/tree/main/Generals/Code/Libraries/Source/Compression/EAC)<br>[OmniBlade](https://github.com/TheAssemblyArmada/Thyme/blob/develop/src/game/common/compression/refpack.cpp)<br>[KUDr](https://github.com/MicaelJarniac/RefPack-Tool)<br>[J.M. Pescado](https://gist.github.com/uyjulian/bd24b98a4c97b775c9ab) |
| Go | [marcboudreau](https://github.com/marcboudreau/godbpf/blob/master/qfs/qfs.go) |||
| Java | [emd4600](https://github.com/emd4600/SporeModder-FX/blob/master/src/sporemodder/file/dbpf/RefPackCompression.java) | [java_dwarf](https://github.com/memo33/jDBPFX/blob/master/src/jdbpfx/util/DBPFPackager.java) ||
| JavaScript | [sebamarynissen](https://github.com/sebamarynissen/qfs-compression) |||
| Kotlin ||| [DarkAtra](https://github.com/DarkAtra/bfme2-modding-utils/blob/main/refpack/src/main/kotlin/de/darkatra/bfme2/refpack/RefPackInputStream.kt)[^3] |
| PHP || [Delphy](https://modthesims.info/wiki.php?title=DBPF_Compression#Example_Code)[^3] ||
| Python || [lingeringwillx](https://github.com/lingeringwillx/sims2lib/blob/main/dbpf.py)[^4]<br>[lah7](https://github.com/lah7/sims2-4k-ui-patch/blob/master/sims2patcher/qfs.py) ||
| Rust || [actioninja](https://github.com/actioninja/refpack-rs) | [actioninja](https://github.com/actioninja/refpack-rs) |
| Scala || [memo33](https://github.com/memo33/scdbpf/blob/master/src/main/scala/scdbpf/internal/QfsCompression.scala) ||

[^1]: [Command and Conquer: Generals - Zero Hour source code](https://github.com/electronicarts/CnC_Generals_Zero_Hour/blob/main/Generals/Code/Libraries/Source/Compression/EAC/refabout.cpp)

[^2]: [Sega Retro](https://segaretro.org/RefPack_compression)

[^3]: Decompression code only

[^4]: C bindings