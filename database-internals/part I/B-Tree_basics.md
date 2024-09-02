
#### <span style="color: #f2cf4a; font-family: Babas; font-size: 3em;">Database internals</span>

##### <span style="color: #f2cf4a; font-family: Babas; font-size: 2em;">Chapter 2</span>

### B-tree Basics
&nbsp;

- One of the most popular storage structures is a B-Tree. Many open source database systems are B-Tree based, and over the years they’ve proven to cover the majority of use cases.
- Before we dive into B-Trees, let’s first talk about why we should consider alternatives to traditional search trees, such as, for example, binary search trees, 2-3-Trees, and AVL Trees

##### Binary search Tree
- A binary search tree (BST) is a sorted in-memory data structure, used for efficient key-value lookups. BSTs consist of multiple nodes. Each tree node is represented by a key, a value associated with this key, and two child pointers (hence the name binary). BSTs start from a single node, called a root node. There can be only one root in the tree. Each node splits the search space into left and right subtrees, as Figure 2-2 shows: a node key is greater than any key stored in its left subtree and less than any key stored in its right subtree
- Tree Balancing:
    - Insert operations do not follow any specific pattern, and element insertion might lead to the situation where the tree is unbalanced
    - The balanced tree is defined as one that has a height of log2(N),where N is the total number of items in the tree, and the difference in height between the two subtrees is not greater than one. Without balancing, we lose performance benefits of the binary search tree structure, and allow insertions and deletions order to determine tree shape.
    - One of the ways to keep the tree balanced is to perform a rotation step after nodes are added or removed. If the insert operation leaves a branch unbalanced (two consecutive nodes in the branch have only one child), we can rotate nodes around the middle one. during rotation the middle node (3), known as a rotation pivot, is promoted one level higher, and its parent becomes its right child.
    - <img src=../images/figure2-4.png title="figure2-4">
    - left rotate algorithm for curiosity:
    - <img src=../images/left-rotate-algo.png>
    - As previously mentioned, unbalanced trees have a worst-case complexity of O(N).Balanced trees give us an average O(log2 N).At the same time,due to low fanout (fanout is the maximum allowed number of children per node), we have to perform balancing, relocate nodes, and update pointers rather frequently. Increased maintenance costs make BSTs impractical as on-disk data structures
    - If we wanted to maintain a BST on disk, we’d face several problems. One problem is **locality**: since elements are added in random order, there’s no guarantee that a newly created node is written close to its parent, which means that node child pointers may span across several disk pages. We can improve the situation to a certain extent by modifying the tree layout and using paged binary trees
    - Another problem, closely related to the cost of following child pointers, is tree height. Since binary trees have a fanout of just two, height is a binary logarithm ofthenumberoftheelementsinthetree,andwehavetoperformO(log2 N)
    seeks to locate the searched element and, subsequently, perform the same number of disk transfers. 2-3-Trees and other low-fanout trees have a similar limitation: while they are useful as in-memory data structures, small node size makes them impractical for external storage

- Considering these factors, a version of the tree that would be better suited for disk implementation has to exhibit the following properties:
    - High fanout to improve locality of the neighboring keys.
    - Low height to reduce the number of seeks during traversal.
- Fanout and height are inversely correlated: the higher the fanout, the lower the height. If fanout is high, each node can hold more children, reducing the number of nodes and, subsequently, reducing height.

##### Disk-Based Structures
- Hard disk drives:
    - On spinning disks, *seeks* increase costs of random reads because they require disk rotation and mechanical head movements to position the read/write head to the desired location. However, once the expensive part is done, reading or writing contiguous bytes (i.e., sequential operations) is relatively cheap.
    - The smallest transfer unit of a spinning drive is a sector, so when some operation is performed, at least an entire sector can be read or written. Sector sizes typically range from 512 bytes to 4 Kb.
    - Head positioning is the most expensive part of an operation on the HDD. This is one of the reasons we often hear about the positive effects of sequential I/O: reading and writing contiguous memory segments from disk.
- Solid State drives:
    - Solid state drives (SSDs) do not have moving parts: there’s no disk that spins, or head that has to be positioned for the read. A typical SSD is built of memory cells, connected into strings (typically 32 to 64 cells per string), strings are combined into arrays, arrays are combined into pages, and pages are combined into blocks
    - Depending on the exact technology used, a cell can hold one or multiple bits of data. Pages vary in size between devices, but typically their sizes range from 2 to 16 Kb. Blocks typically contain 64 to 512 pages. Blocks are organized into planes and, finally, planes are placed on a die. SSDs can have one or more dies. Figure 2-5 shows this hierarchy.
    - <img src=../images/figure2-5.png />
    - The smallest unit that can be written (programmed) or read is a *page*
    - 
&nbsp;

- Since in both device types (HDDs and SSDs) we are addressing chunks of memory rather than individual bytes (i.e., accessing data block-wise), most operating systems have a block device abstraction. It hides an internal disk structure and buffers I/O operations internally, so when we’re reading a single word from a block device, the whole block containing it is read.

## Ubiquitous B-Trees
- B-Trees can be thought of as a vast catalog room in the library: you first have to pick the correct cabinet, then the correct shelf in that cabinet, then the correct drawer on the shelf, and then browse through the cards in the drawer to find the one you’re searching for. Similarly, a B-Tree builds a hierarchy that helps to navigate and locate the searched items quickly.
- B-Trees build upon the foundation of balanced search trees and are different in that they have higher fanout (have more child nodes) and smaller height.
- B-Trees are sorted: keys inside the B-Tree nodes are stored in order. Because of that, to locate a searched key, we can use an algorithm like binary search. This also implies that lookups in B-Trees have logarithmic complexity. For example, finding a searched key among 4 billion (4 × 109) items takes about 32 comparisons. If we had to make a disk seek for each one of these comparisons, it would significantly slow us down, but since B-Tree nodes store dozens or even hundreds of items, we only have to make one disk seek per level jump.
- Using B-Trees, we can efficiently execute both point and range queries. Point queries, expressed by the equality (=) predicate in most query languages, locate a single item. On the other hand, range queries, expressed by comparison (<, >, ≤, and ≥) predicates, are used to query multiple data items in order.
- B-Trees consist of multiple nodes. Each node holds up to N keys and N + 1 pointers to the child nodes
- Since B-Trees are a page organization technique (i.e., they are used to organize and navigate fixed-size pages), we often use terms node and page interchangeably.
- The relation between the node capacity and the number of keys it actually holds is called occupancy.
- B-Trees are characterized by their fanout: the number of keys stored in each node. Higher fanout helps to amortize the cost of structural changes required to keep the tree balanced and to reduce the number of seeks by storing keys and pointers to child nodes in a single block or multiple consecutive blocks.
- *Balancing operations* (namely, splits and merges) are triggered when the nodes are full or nearly empty
- in B-Trees, Internal nodes store only *separator keys* used to guide the search algorithm to the associated value stored on the leaf level.
#### separator keys:
- Keys stored in B-Tree nodes are called index entries, separator keys, or divider cells. They split the tree into subtrees (also called branches or subranges), holding corresponding key ranges. Keys are stored in sorted order to allow binary search. A subtree is found by locating a key and following a corresponding pointer from the higher to the lower level.
- <img src=../images/figure2-10.png>
&nbsp;
- Some B-Tree variants also have sibling node pointers, most often on the leaf level, to simplify range scans. These pointers help avoid going back to the parent to find the next sibling. Some implementations have pointers in both directions, forming a double-linked list on the leaf level, which makes the reverse iteration possible.
#### B-Tree Lookup Complexity:
- B-Tree lookup complexity can be viewed from two standpoints: the number of block transfers and the number of comparisons done during the lookup.
    - transfer:
        - In terms of number of transfers, the logarithm base is N (number of keys per node). There are K times more nodes on each new level, and following a child pointer reduces the search space by the factor of N. During lookup, at most logK M (where M is a total number of items in the B-Tree) pages are addressed to find a searched key. The number of child pointers that have to be followed on the root- to-leaf pass is also equal to the number of levels, in other words, the height h of the tree.
    - number of comparison:
        - From the perspective of number of comparisons, the logarithm base is 2, since searching a key inside each node is done using binary search. Every comparison halvesthesearchspace,socomplexityislog2 M.
- Knowing the distinction between the number of seeks and the number of comparisons helps us gain the intuition about how searches are performed and understand what lookup complexity is, from both perspectives.
- B-Tree lookup complexity is generally referenced as log M. Logarithm base is generally not used in complexity analysis, since changing the base simply adds a constant factor, and multiplication by a constant factor does not change complexity For example, given the nonzero constant factor c, O(|c| × n) == O(n)
- B-Tree node splits:  To perform a split, we first create a new node and move elements starting from index N/2 + 1 to it. The split point key is promoted to the parent.
- node splits are done in four steps:
    - Allocate a new node.
    - Copy half the elements from the splitting node to the new one.
    - Place the new element in to the corresponding node.
    - At the parent of the split node,add a separator key and a pointer to the new node.
- B-Tree node merges: f two adjacent nodes have a common parent and their contents fit into a single node, their contents should be merged (concatenated); if their contents do not fit into a single node, keys are redistributed between them to restore balance
    - For leaf nodes: if a node can hold up to N key-value pairs, and a combined number of key-value pairs in two neighboring nodes is less than or equal to N.
    - For nonleaf nodes: if a node can hold up to N + 1 pointers, and a combined number of pointers in two neighboring nodes is less than or equal to N + 1.
- node merges are done in three steps:
    - Copy all elements from the right node to the left one
    - Remove the right node pointer from the parent(or demote it in the caseof a nonleaf merge).
    - Remove the right node.
- One of the techniques often implemented in B-Trees to reduce the number of splits and merges is **rebalancing**.


##### <span style="color: #f2cf4a; font-family: Babas; font-size: 2em;">Summary</span>
- In this chapter, we started with a motivation to create specialized structures for on-disk storage. Binary search trees might have similar complexity characteristics, but still fall short of being suitable for disk because of low fanout and a large number of relocations and pointer updates caused by balancing. B- Trees solve both problems by increasing the number of items stored in each node (high fanout) and less frequent balancing operations.
After that, we discussed internal B-Tree structure and outlines of algorithms for lookup, insert, and delete operations. Split and merge operations help to restructure the tree to keep it balanced while adding and removing elements. We keep the tree depth to a minimum and add items to the existing nodes while there’s still some free space in them.
We can use this knowledge to create in-memory B-Trees. To create a disk-based implementation, we need to go into details of how to lay out B-Tree nodes on disk and compose on-disk layout using data-encoding formats.
