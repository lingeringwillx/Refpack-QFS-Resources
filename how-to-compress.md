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

1- 0xFB likely stands for Frank Barchard, the developer of the algorithm.

2- In older headers, the uncompressed size is stored in a 3 bytes integer. The compression algorithm is pretty old so it's likely that they assumed that 3 bytes is enough for storing the uncompressed size.

3- The uncompressed size is stored in big endian even in games that natively use little endian. It's possible that the algorithm's standard specifies that it should be like this.

4- I have not seen the 0b00000001 flag in any game files. It's likely never or rarely used. However, reverse engineering efforts did show that the algorithm supports this flag.

5- If flag 0b01000000 is set, then the maximum offset is limited to a specific value, exceeding this value might cause the game to crash. This flag is usually ignored in modder made implementations of the algorithm as it doesn't affect decompression, and when compressing a file it's just left unset.

## Compression

1- Create an array to hold the compressed data. The size of the array is initially going to be about the same as the original uncompressed data.

Note: For small files, the compressed data could end up being longer than the uncompressed data (header + some control characters + no reduced size from compression). You would need to account for this either by creating an array that's slighty longer than the uncompressed data, or by simply discarding the compressed output and keeping the file uncompressed.

2- Find common patterns within the file. There is a variey of ways to achieve this, but this is commonly done using hash chaining. [Here is a demonstration of various ways to implement this](https://github.com/lingeringwillx/CrappySims2Compression/blob/main/practice/).

3- Loop over the file and check the pattern in the current location against the previous locations. Find the longest matching pattern, or at least find a match of a good length. The match has to be within the length and offset boundaries specified by the compression algorithm.

4- There are primarily two modes in Refpack compression:

a- When a match is found, a number of bytes is appended to the compressed data indicating the length of the match, and the offset from the current position where the match could be found.

b- When there is no match, the data is copied from the uncompressed data with no compression. A byte is appended before the data indicating the length of the data. 

6- Write the compression header to the beginning of the compressed data.

7- Slice the array to the size that you've got after compression.

### Encoding

Bit operation are done to mask off unrelated bits or shift the values to a specific position. Addition and subtraction are done because they allow the algorithm to encode a few more bytes compared to if it didn't. Addition and subtraction could also be used to switch certain bits on or off.

Note: Some guides mistakenly claim that SimCity 4 has its own unique encoding. This is wrong.

The three variables that we need to encode are:

**literal/plain:** The number of bytes to copy from the compressed data when no match is found.

**offset:** The distance between the two matches. (i.e. current position - match position).

**count:** The length of the match.

#### References

When a match is found, we simply append a number of bytes indicating the length and offset of the match, as well as the number of literals preceding the match.

The 1-3 literals from the uncompressed data should be appended after the reference.

Subtract the offset by 1 before before doing anything with it. This is a part of the encoding applied to the offset:

`offset = offset - 1`

##### Short

The minimum length for the matching pattern should be 3 (otherwise this compression is not useful).

```C
//Bits: 0oocccpp oooooooo
// 0 <= literal <= 3, 3 <= count <= 10, 1 <= offset <= 1024

b1 = ((offset >> 3) & 0b01100000) | ((count - 3) << 2) | literal
b2 = offset
```

##### Long

The minimum length for the matching pattern should be 4 (otherwise this compression is not useful).

```C
//Bits: 10cccccc ppoooooo oooooooo
// 0 <= literal <= 3, 4 <= count <= 67, 1 <= offset <= 16384

b1 = 0b10000000 | (count - 4)
b2 = (literal << 6) | (offset >> 8)
b3 = offset
```

##### Very Long

The minimum length for the matching pattern should be 5 (otherwise this compression is not useful).

```C
//Bits: 110occpp oooooooo oooooooo cccccccc
// 0 <= literal <= 3, 5 <= count <= 1028, 1 <= offset <= 131072

b1 = 0b11000000 | ((offset >> 12) & 0b00010000) | (((count - 5) >> 6) & 0b00001100) | literal
b2 = offset >> 8
b3 = offset
b4 = count - 5
```

#### Literals

Used when no match is found. A single byte is appended indicating the length of the data, followed by the data itself.

##### Literal

This can be added multiple times until you're 0-3 bytes behind the location of a match.

Note: This must be a multiple of four due to the 2 bit right shift truncating the last two bits.

```C
//Bits: 111ppp00
// 4 <= literal <= 112, count = 0, offset = 0

b1 = 0b11100000 | ((literal - 4) >> 2)
```

##### EOF

Added to the end of the compressed data if you still need to append 1-3 bytes from the uncompressed data.

```C
//Bits: Bits: 111111pp
// 0 <= literal <= 3, count = 0, offset = 0

b1 = 0b11111100 | literal
```

## Decompression

1- Create a new array to hold the decompressed data. The size of the decompressed data can be found in the compression header.

2- Read the first byte (b1 in the code samples below).

3- Read additional characters based on the value of the first character (up to three additional bytes depending on the value of the first byte).

4- Use bit operations to get the information about the match length, offset, and literals.

5- Copy the number of literals specified from the compressed data to the decompressed data.

6- Go back to the offset specified in the decompressed data and copy the number of bytes specified.

Note: The copying in step 6 has to be done one byte at a time, otherwise decompression will be fail in cases when the number of bytes to copy is larger than the offset (The compression algorithm allows this).

### Decoding

The value of the first byte indicates the decompression method that should be applied. It is listed in the headers of each of the following sections.

Note: Some guides mistakenly claim that SimCity 4 has its own unique encoding. This is wrong.

There are two copy modes in RefPack decompression:

#### Offset Copy

1- Copy a few bytes from the compressed data based on the decoded number of literals ([Literal Copy](#Literal-Copy)).

2- Copy bytes from the *decompressed data* from the specified offset.

##### Short (0x00 - 0x7F)

Read one additional character.

The following bit operations can be applied to get the numbers that we need:

```C
//Bits: 0oocccpp oooooooo

literal = b1 & 0b00000011 //0-3
count = ((b1 & 0b00011100) >> 2) + 3 //3-11
offset = ((b1 & 0b01100000) << 3) + b2 + 1 //1-1024
```

##### Long (0x80 - 0xBF)

Read two additional characters.

```C
//Bits: 10cccccc ppoooooo oooooooo

literal = ((b2 & 0b11000000) >> 6 //0-3
count = (b1 & 0b00111111) + 4 //4-67
offset = ((b2 & 0b00111111) << 8) + b3 + 1 //1-16384
```

##### Very Long (0xC0 - 0xDF)

Read three additional characters.

```C
//Bits: 110occpp oooooooo oooooooo cccccccc

literal = b1 & 0b00000011 //0-3
count = ((b1 & 0b00001100) << 6) + b4 + 5 //5-1028
offset = ((b1 & 0b00010000) << 12) + (b2 << 8) + b3 + 1 //1-131072
```

#### Literal Copy

A single byte followed by an uncompressed bytes block. They should be copied to the decompressed data.

##### Literal (0xE0 - 0xFB)

```C
//Bits: 111ppp00

literal = ((b1 & 0b00011111) << 2) + 4 //4-112
count = 0
offset = 0
```

##### EOF (0xFD - 0xFF)

```C
//Bits: Bits: 111111pp

literal = b1 & 0b00000011 //0-3
copy = 0
offset = 0
```
