
<span style="color: #f2cf4a; font-family: Babas; font-size: 3em;">Database internals</span>

<span style="color: #f2cf4a; font-family: Babas; font-size: 2em;">Chapter 3</span>

### File Formats:
&nbsp;

- rom an application developer’s perspective, memory accesses are mostly transparent. Because of virtual memory , we do not have to manage offsets manually. Disks are accessed using system calls --> [wanna know more](https://databass.dev/links/54). We usually have to specify the offset inside the target file, and then interpret on-disk representation into a form suitable for main memory.
- The semantics of pointer management in on-disk structures are somewhat different from in-memory ones. It is useful to think of on-disk B-Trees as a page management mechanism: algorithms have to compose and navigate pages. Pages and pointers to them have to be calculated and placed accordingly.
- Creating a file format is in many ways similar to how we create data structures in languages with an unmanaged memory model. We allocate a block of data and slice it any way we like, using fixed-size primitives and structures. If we want to reference a larger chunk of memory or a structure with variable size, we use pointers.
- Languages with an unmanaged memory model allow us to allocate more memory any time we need (within reasonable bounds) without us having to think or worry about whether or not there’s a contiguous memory segment available, whether or not it is fragmented, or what happens after we free it. On disk, we have to take care of garbage collection and fragmentation ourselves.
##### Binary Encoding:
- To store data on disk efficiently, it needs to be encoded using a format that is compact and easy to serialize and deserialize. When talking about binary formats, you hear the word *layout* quite often. Since we do not have primitives such as *malloc* and *free*, but only *read* and *write*, we have to think of accesses differently and prepare data accordingly.
- Before we can organize records into pages, we need to understand how to represent keys and data records in binary form, how to combine multiple values into more complex structures, and how to implement variable-size types and arrays.
- Primitve types:
    - Keys and values have a type, such as integer, date, or string, and can be represented (serialized to and deserialized from) in their raw binary forms.
- Records consist of primitives like numbers, strings, booleans, and their combinations. However, when transferring data over the network or storing it on disk, we can only use byte sequences. This means that, in order to send or write the record, we have to serialize it (convert it to an interpretable sequence of bytes) and, before we can use it after receiving or reading, we have to deserialize it (translate the sequence of bytes back to the original record).
- In binary data formats, we always start with primitives that serve as building blocks for more complex structures. Different numeric types may vary in size. byte value is 8 bits, short is 2 bytes (16 bits), int is 4 bytes (32 bits), and long is 8 bytes (64 bits).
- *Floating-point numbers* (such as float and double) are represented by their sign, fraction, and exponent
- All primitive numeric types have a fixed size. Composing more complex values together is much like struct2 in C. You can combine primitive values into structures and use fixed-size arrays or pointers to other memory regions.
Strings and other variable-size data types (such as arrays of fixed-size data) can be serialized as a number, representing the length of the array or string, followed by size bytes: the actual data. For strings, this representation is often called UCSD String or Pascal String, named after the popular implementation of the Pascal programming language. We can express it in pseudocode as follows:
String {

    size uint16

    data byte[size]

    }
- Just like packed booleans, flag values can be read and written from the packed value using bitmasks and bitwise operators. For example, in order to set a bit responsible for one of the flags, we can use bitwise OR (|) and a bitmask. Instead of a bitmask, we can use bitshift (<<) and a bit index. To unset the bit, we can use bitwise AND (&) and the bitwise negation operator (~). To test whether or not the bit n is set, we can compare the result of a bitwise AND with 0:
<img src=../images/bit1.png>
<img src=../images/bit2.png>
&nbsp;

#### General Principles
- Usually, you start designing a file format by deciding how the addressing is going to be done: whether the file is going to be split into same-sized pages, which are represented by a single block or multiple contiguous blocks. Most in-place update storage structures use pages of the same size, since it significantly simplifies read and write access. Append-only storage structures often write data page-wise, too: records are appended one after the other and, as soon as the page fills up in memory, it is flushed on disk.
The file usually starts with a fixed-size header and may end with a fixed-size trailer, which hold auxiliary information that should be accessed quickly or is required for decoding the rest of the file. The rest of the file is split into pages.
- Many data stores have a fixed **schema**, specifying the number, order, and type of fields the table can hold. having a fixed schema helps to reduce the amount of data stored on disk: instead of repeatedly writing field names, we can use their positional identifiers.
- Building more complex structures usually involves building hierarchies: fields composed out of primitives, cells composed of fields, pages composed of cells, sections composed of pages, regions composed of sections, and so on. There are no strict rules you have to follow here, and it all depends on what kind of data you need to create a format for.
Database files often consist of multiple parts, with a lookup table aiding navigation and pointing to the start offsets of these parts written either in the file header, trailer, or in the separate file.
#### Page Structure:
- Database systems store data records in data and index files. These files are partitioned into fixed-size units called pages, which often have a size of multiple filesystem blocks. Page sizes usually range from 4 to 16 Kb.
Let’s take a look at the example of an on-disk B-Tree node. From a structure perspective, in B-Trees, we distinguish between the leaf nodes that hold keys and data records pairs, and nonleaf nodes that hold keys and pointers to other nodes. Each B-Tree node occupies one page or multiple pages linked together, so in the context of B-Trees the terms node and page (and even block) are often used interchangeably.
- 