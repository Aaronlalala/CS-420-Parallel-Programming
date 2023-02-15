# Introduction to Caches

## Row-Major Order and Column-Major Order

In C/C++ matrices are stored in row-major order by default. There are two ways to access values in a matrix:

```c++
int temp = 0;
for (int i = 0; i < N; i++) {
  for (int j = 0; j < N; j++) {
    temp += B[i][j];
  }
}

for (int j = 0; j < N; j++) {
  for (int i = 0; i < N; i++) {
    temp += B[i][j];
  }
}
```

The first loop (row-major) performs faster. Its elements in a matrix are stored in continuous memory locations. 

For example a 2-d array: *a = 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16

When fetch $a[1][1]$, due to spatial locality, cache will fetch a block a continue data. And due to temporal locality, cache will stored fetched data for a while. Then, when fetch $a[1][2]$, since data is prefetched and stored in the cache, no need to access the main memory.

**Why cache cannot fetch elements in the same columns?** Because values in the same columns are not stored in a continuous memory location.

**What is ALU?**  ALU stands for Arithmetic Logic Unit. It is a digital circuit in a computer's central processing unit (CPU) that performs arithmetic and logical operations. 

## Cache and Register

A register is a small amount of fast memory within CPU. However, its space is limited. Cache is a type of memory that is closer to the CPU than the main memory. Thus, it's faster than main memory. 

Speed: register > cache > main memory.

## SRAM and DRAM

SRAM (Static random Access Memory) retains data without the need for a constant refresh signal. It's faster and more expensive than DRAM. Typically used for cache. It's on chip.

DRAM (Dynamic) stores each bit of data in a separate capacitor within a memory cell. It has higher density. It must constantly refresh to maintain the stored data.

# Cache Organization

## Cache Components

**Cache size:** the amount of memory available for a cache.

**Cache set:** each set contains one or more data blocks. Direct-mapping cache set can only have 1 data block for each.

**Block:** the basic unit for cache storage. May contain multiple bytes/words of data. The same thing as cache line. Generaly 64 or 128 bytes.

**Tag:** tag is the unique identifier for each data block stored in the cache. It is used to compare with the memory address to indicate whether data is already stored in the cache.

**Tag bits:** memory that is used to store the information of tags. It depends on cache size, block size and number of sets.

**Cache address:** set index + tag bits + offset bits. Set index indicates where the block stored. It depends on the number of sets. Tag bits are used to find the block in that set. And offset bits tell the exact location within the block. The number of offset bits depends on block size.

<img src="Figures/Cache Address.png" alt="Cache Address" style="zoom: 67%;" />



## Direct Mapping

Each block of memory can be one-to-one mapped to exactly one cache set. It is simplest and fastest. But it has the least flexibility and will increase cache conflicts, resulting decrease cache hit rate.

[Example](https://www.youtube.com/watch?v=RqKeEIbcnS8)

## Set Associative Mapping

Each block of memory can be mapped to several cache sets. It's many-to-one mapping. This design increase the hit rate after initialization.

[Example](https://www.youtube.com/watch?v=quZe1ehz-EQ)

**Why direct mapping create conflicts?**

Since for one specific cache set, it only stores one memory block. When two or more memory blocks' address calculated (set index = memory address / block size % total number of sets) to the same set index, it will overwrite the previous results. One-to-one mapping means one cache set stores only one block at the time. Does not mean that it can only store one specific memory block.

**Why Associative Mapping will not create conflicts?**

Each set can hold multiple blocks. This means that when two blocks have the same index, they will both map to the same cache set. But they are stored in different cache lines within the set.

## Cache Miss

Processor looks for data block in the cache but cannot find. It must retrieve it from slower main memory.

## Four Types of Cache Miss

**Compulsory misses:** A miss when the cache line is accessed for the first time. Cold miss.

**Capacity misses:** No space in cache.

**Conflict misses:** The idea is that hits in a fully associative cache that become misses in an set-associative cache. Conflict misses are those that occur going from fully associative to eight-way associative, four-way associative, and so on.
