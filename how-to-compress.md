## Compression Header

Various headers for the RefPack compression algorithm are used in EA's games:

#### Header 1 (90s Games)

```
1 byte flags:

bit 0b00010000: Always set

bit 0b00000001: compressed size added to header

1 byte magic: 0xFB

if flag 0b00000001 set: 3 bytes int compressed size (big endian)

3 bytes int uncompressed size (big endian)
```

According to [Martin Korth](https://problemkaputt.de/psxspx-cdrom-file-compression-ea-methods.htm), other files can be found in some games containing the magic character 0xFB in the header. However, these flags indicate that the compression used is not RefPack:

| Flag | Compression |
|-|-|
| 0x30, 0x32, 0x34 | Huffman Encoding |
| 0x46 | Byte-Pair Encoding |
| 0x4A | Run-Length Encoding |
| 0xC0 | File Archive (Not a compression) |

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

1- 0xFB likely stands for Frank Barchard.

2- In older headers, the uncompressed size is stored in a 3 bytes integer. The compression algorithm is pretty old so it's likely that they assumed that 3 bytes is enough for storing the uncompressed size.

3- The uncompressed size is stored in big endian even in games that natively use little endian. It's possible that the algorithm's standard specifies that it should be like this.

4- I have not seen the 0b00000001 flag in any game files. It's likely never or rarely used. However, reverse engineering efforts did show that the algorithm supports this flag.

5- If flag 0b01000000 is set, then the maximum offset is limited to a specific value, exceeding this value might cause the game to crash. This flag is usually ignored in modder made implementations of the algorithm as it doesn't affect decompression, and when compressing a file it's just left unset.

## Compression

1- Create an array to hold the compressed data. The size of the array is initially going to be about the same as the original uncompressed data.

Note: For small files, the compressed data could end up being longer than the uncompressed data (header + some control characters + no reduced size from compression). You would need to account for this either by creating an array that's slighty longer than the uncompressed data, or by simply discarding the compressed output and keeping the file uncompressed.

2- Find common patterns within the file. There is a variey of ways to achieve this, but this is commonly done using hash chaining. [Here is a demonstration of various ways to implement this](https://github.com/lingeringwillx/CrappySims2Compression/blob/main/practice/).

3- Loop over the file and check the pattern in the current location against the previous locations. Find the longest matching pattern, or at least find a match of a good length. The match has to be within the length and offset boundaries specified by the compression algorithm.

4- Once you found a match, add literal control characters a long with their bytes to the compressed data until you've reached the location of the match, then convert the length and offset of the match to control characters and add them to the compressed output.

5- Repeat this until you've added the last match. After that add literal and EOF control characters along with their respective bytes until you reach the end of the uncompressed data.

6- Write the compression header to the beginning of the compressed data.

7- Slice the array to the size that you've got after compression.

Typical structure of compressed data: `control_characters literals_from_decompressed_data  control_characters  literals_from_decompressed_data...`

#### Control Characters

Note: Some guides mistakenly claim that SimCity 4 has its own unique control character encoding. This is wrong.

**The three variables that we need to encode are:**

**literal/plain** = The number of bytes to copy from the compressed data as is.

**offset** = The offset in the *decompressed* data to copy the bytes from (i.e. current position - match position).

**count** = The number of bytes to copy from the offset in the *decompressed* data

Subtract the offset by 1 before before doing anything with it. This is a part of the transformations applied to the offset:

`offset = offset - 1`

Bit operation are usually done to mask off unrelated bits or shift the values to a specific position. Addition and subtraction are done because they allow the algorithm to encode a few more bytes compared to if it didn't. Addition and subtraction could also be used to switch certain bits on or off.

##### Short

The minimum length for the matching pattern should be 3 (otherwise this compression is not useful).

```C
//Bits: 0oocccpp oooooooo
// 0 <= literal <= 3, 3 <= count <= 10, 1 <= offset <= 1024

b0 = ((offset >> 3) & 0b01100000) + ((count - 3) << 2) + literal
b1 = offset
```

##### Medium

The minimum length for the matching pattern should be 4 (otherwise this compression is not useful).

```C
//Bits: 10cccccc ppoooooo oooooooo
// 0 <= literal <= 3, 4 <= count <= 67, 1 <= offset <= 16384

b0 = 0b10000000 + (count - 4)
b1 = (literal << 6) + (offset >> 8)
b2 = offset
```

##### Long

The minimum length for the matching pattern should be 5 (otherwise this compression is not useful).

```C
//Bits: 110occpp oooooooo oooooooo cccccccc
// 0 <= literal <= 3, 5 <= count <= 1028, 1 <= offset <= 131072

b0 = 0b11000000 + ((offset >> 12) & 0b00010000) + (((count - 5) >> 6) & 0b00001100) + literal
b1 = offset >> 8
b2 = offset
b3 = count - 5
```

##### Literal

Copy as is from the uncompressed data without compression. Can be added multiple times until you're 1-3 bytes behind the location of a match.

Note: This must be a multiple of four due to the 2 bit right shift truncating the last two bits.

```C
//Bits: 111ppp00
// 4 <= literal <= 112, count = 0, offset = 0

b0 = 0b11100000 + ((literal - 4) >> 2)
```

##### EOF

Added to the end of the compressed data if you still need to add 1-3 bytes to be copied as is.

```C
//Bits: Bits: 111111pp
// 0 <= literal <= 3, count = 0, offset = 0

b0 = 0b11111100 + literal
```

## Decompression

1- Create a new array to hold the decompressed data. The size of the decompressed data can be found in the compression header.

2- Read the first control character.

3- Read additional control characters based on the value of the first control character

4- Use bit operations to get information about the number of bytes to copy as is, the offset, and the number of bytes to copy from the offset.

5- Copy the number of bytes specified to be copied as is from the compressed data to the decompressed data.

6- Go back to the offset in the decompressed data and copy the number of bytes specified.

Note: The copying in step 6 has to be done one byte at a time, otherwise decompression will be incorrect in cases when the number of bytes to copy is larger than the offset (The compression allows this).

Typical structure of decompressed data:

When there is an offset copy: `literals_from_compressed_data  bytes_from_offset  literals_from_compressed_data  bytes_from_offset...`

When copying plainly: `literals_from_compressed_data  literals_from_compressed_data ...`

#### Control Characters

Note: Some guides mistakenly claim that SimCity 4 has its own unique control character encoding. This is wrong.

##### Short (0x00 - 0x7F)

Has two control characters b0 and b1. Their bits are listed in order below.

The following bit operations can be applied to get the numbers that we need:

```C
//Bits: 0oocccpp oooooooo

literal = b0 & 0b00000011 //0-3
count = ((b0 & 0b00011100) >> 2) + 3 //3-11
offset = ((b0 & 0b01100000) << 3) + b1 + 1 //1-1024
```

##### Medium (0x80 - 0xBF)

Has three control characters.

```C
//Bits: 10cccccc ppoooooo oooooooo

literal = ((b1 & 0b11000000) >> 6 //0-3
count = (b0 & 0b00111111) + 4 //4-67
offset = ((b1 & 0b00111111) << 8) + b2 + 1 //1-16384
```

##### Long (0xC0 - 0xDF)

Has four control characters.

```C
//Bits: 110occpp oooooooo oooooooo cccccccc

literal = b0 & 0b00000011 //0-3
count = ((b0 & 0b00001100) << 6) + b3 + 5 //5-1028
offset = ((b0 & 0b00010000) << 12) + (b1 << 8) + b2 + 1 //1-131072
```
##### Literal (0xE0 - 0xFB)

Has only one control character. This one only involves literal/plain copy with no copying from the offset.

```C
//Bits: 111ppp00

literal = ((b0 & 0b00011111) << 2) + 4 //4-112
count = 0
offset = 0
```

##### EOF (0xFD - 0xFF)

Has only one control character. This is used for the remaining portion of the compressed data at the end.

```C
//Bits: Bits: 111111pp

literal = b0 & 0b00000011 //0-3
copy = 0
offset = 0
```
