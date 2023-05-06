[toc]

# Level of Parallelism 

**Thread:** smallest computing unit within the process.

**Core:** is the hardware that executes thread. Threads within a core share computing resources. Threads in different cores only share memory.

**Process:** is an instance of running program that is being executed by one or more threads.

**Shared memory parallelism:** use one processor. However, each thread within the process compute simutaneously. (OpenMP).

**Distributed memory parallelism:** many processors connected together. Memory is not shared by processors. Need to exchange data. (MPI)

# Amdahl’s Law

It describes the limit of the parallel computing, saying that the benefits are diminishing. $\text{speed up} = \dfrac{1}{1 - p + \frac{p}{n}}$. $p$ denotes the proportion of the computing that could be parallelized. $n$ denotes the number of threads or processors that apply for the parallel computation. Genearlly, assume the computation amount is 1 which is the numerator. The denominator is the sum of  sequential computation + parallel computation that is allocated for each thread or processor.

# Pipelining

Consider the example of constructing a house. After x days, every one house is constructed for a single day. 

Pipeline bubble: some stages in a pipeline might be dependent with each other, thus, cannot be parallelized.

## Dependencies

Intruction stream might be dependent.

Three classes of dependencies: 

1. data dependency: false data dependency could be solved by loop unrolling, temporary variable.

2. control dependency: if else condition, for loop. Could use branch prediction to solve.

3. structure dependency

## Loop Unroll

Dependency distance: the distance between two dependent data. For example, a[i]  = a[i-1] * 2 has dependency distance 1. a[i] = a[i-3] * 2 has dependency distance 3. For dependency distance greater than 1, we still have chance to utilize the pipeline at percentage$\dfrac{d}{m}$. We could unroll the loop as follows:

```c++
int main() {
  
    const int dependency_distance = 2;
    std::vector<int> a(n);
  	/**
  	* Some initilization set up
  	*/

    // Unroll and parallelize the loop
    std::vector<int> b(n);
    b[0] = 1;
  
  	// Fewer branches and can pipeline
    for (int i = 1; i < n; i += dependency_distance) {
      	// computation 1 and computation 2 are not dependent with each other. Utilize 2 stages of pipeline.
      
      	// computation 1
        b[i] = b[i - 2] * constant;
      	// computation 2
        if (i + 1 < n) {
            b[i + 1] = b[i - 1] * constant;
        }
    }
    return 0;
}
```

## Anti Dependence

Earlier value is dependent on later value

```c++
int main() {
  for (int i = 0; i < N; i++) {
    a[i] = constant * a[i + 1];
  }
  // The above code seems dependent, however, could be handled by temporary variables
  for (int i = 0; i < N; i+=2) {
    int t1 = a[i + 1];
    int t2 = a[i + 2];
    // The following computations will be parallized.
    a[i] = constant * t1;
    a[i + 1] = constant * t2;
  }
}
```



## Control Dependence

Branch typically refers to a point where the execution flow can diverge. For example, if else condition, for loop (need to check break condition). 

**Branch prediction**:

Processors continue to fetch and execute instruction while waiting for the outcome of branch instruction. If the outcome is different, then it rolls back the program.

## Loop Fusion

If we do some computations in two loops with the same index, a better choice is to combine them.

```c++
for (int i = 0; i < N; i++) {
  a[i] = a[i] + b[i];
}
for (int i = 0; i < N; i++) {
  b[i] = b[i] * s;
}

// We fuse the above two loops
// 1: time wating for checking branch condition is reduced
for (int i = 0; i < N; i++) {
  // 2: After b[i] is fetched, it will be stored in a register temporarily
  // 3: a[i] and b[i] are false dependent. We could use a temporary variable to store b[i], and do the computation
  // parallely.
  a[i] = a[i] + b[i];
  // No need to fetch b[i] again.
  b[i] = b[i] * constant;
}
```



## Code Optimization Summary

- Loop unrolling (some compiler can do it automatically)
- Loop fusion (some compiler can do it automatically)
- Branch prediction is doned by hardware
- Use temporary variable to reduce number of memory fetches



# Caches

## Cache Hit

A cache hit is when the requested data is found in cache. When a system wants some data, it will first look at cache before fetching the main memory.

## Cache Miss

Processor looks for data at cache but cannot find. It must retrieve data from slower main memory.

**Compulsory misses:** A miss when the cache line is accessed for the first time. Because cache is empty. It is also called cold miss.

**Capacity misses**: This occurs when the cache is not large enough to store all the data that the processor needs.

**Conflict Miss:** This occurs when multiple memory addresses map to the same cache index, resulting in cache entries being overwritten. This type of miss can be reduced by increasing the associativity of the cache.

**Coherency Miss:** This occurs in a multi-processor system when one processor updates a memory address that is also present in the cache of another processor. The cache of the other processor needs to be invalidated to maintain coherency, resulting in a cache miss.

## Temporal Locality

The program tends to fetch the same data repeatively in short time.

Caches keep recently accessed data and overwrite the least recent use data so that future accesses to the same data can be served faster.

## Spatial Locality

The program tends to fetch data that are closed to each other in short time.

When processor fetches data in main memory, it will bring data nearby together to the cache.

## Cache organization

**Direct-mapped:** a memory block is mapped to only one cacne line. When another memory block is fetched, previous block will be overwritten.

**Fully associative:** a memory block can be placed in any cache line. When searching for data, the cache must search all cache lines simultaneously to determine if the data is present. This allows for greater flexibility in cache utilization, reducing the chance of cache conflicts. However, it's slower.

**Set-associative:** a cache is divided into several sets. Each set contains many cache lines. Memory block is mapped to set. However, it could be placed in any cache line within the set. It's a midpoint of fully associative and direct-mapped design.

**The reason to have multilevel caches?**

The multilevel chache further discriminant the searching speed. L1 has minimal capacity but fastest speed. This optimization could further increase the overall performance of cache.

## Cache Performance Model

$\dfrac{T}{(1-\beta)T + \beta \tau}$. $T$ is memory access time, $\tau$ is cache access time. $\beta$ is cache hit ratio. Generally, it is without cache time over with cache time.

## Matrix Transpose

Divide matrix into square tiles. Then transpose matrix by block could increase utilization of cache by spatial locality.

## Matrix Multiplication

Also divie matrix into square tiles. Why? Because in naive matrix multiplication, we basically compute vector inner product and fill the result at corresponding position. However, one vector will be multiplied with many other vectors. For large matrix, cache cannot store so much elements, then data is repeatively overwritten and refetched. If we could divide matrix into small blocks. When we do block multiplication, all element could be stored in cache.



# Vector Instructions

## Single Instruction Multiple Data (SIMD)

It's a parallel architecture enables a single instruction to handle multiple data elements simultaneously.

## OpenMP SIMD

C++ and C does not like numpy could directly add vectors in a vectorized way. We could use OpenMP simd to add them.

```c++
// Use OpenMP SIMD to add the arrays element-wise
#pragma omp simd
for (int i = 0; i < ARRAY_SIZE; ++i) {
  c[i] = a[i] + b[i];
}
```

## Data Alignment

When processors access memory in chunks and data is aligned properly, it only takes a single access. However, if data is spanned in two chunks, processors might need to acceess twice to get a full piece of data.



# Architecture

## SMT and Hyperthreading

Simultaneous Multi-Threading and hyperthreading are architecture concepts. Processor without SMT, each core can only execute one thread at a time. When a core is waitng for memory fetch, it is idle. With SMT, processors could utilize those idle time.



Architecture of CPU and GPU. What components are removed and what components are added?

