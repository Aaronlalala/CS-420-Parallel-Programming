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

**Compulsory misses:** it happens because it's the first time to access that block of data.

**Capacity misses**: This occurs when the cache is not large enough to store all the data that the processor needs.

**Conflict Miss:** the block of data has been to the cache earlier. However, it was overwritten by other data.

**Coherency Miss:** This occurs in a multi-processor system when one processor updates a memory address that is also present in the cache of another processor. The cache of the other processor needs to be invalidated to maintain coherency, resulting in a cache miss.



Can compulsory misses be avoided with prefetching? Yes. One main advantage of prefethcing is to reduce the number of compulsory cache misses. Due to spatial locality, when cache will bring target's neighbor block as well.



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

## SMT (Hyperthreading)

Simultaneous Multi-Threading and hyperthreading are architecture concepts. Processor without SMT, each core can only execute one thread at a time. When a core is waitng for memory fetch, it is idle. With SMT, processors could utilize those idle time.

## Multi-Cores

Cores within a processor share the main memory. Each core might have a private cache, and share a higher level cache.

## False Sharing

Example:

1. Thread A updates `data[0]`, causing the cache line containing both `data[0]` and `data[1]` to be loaded into Core A's cache and marked as modified.
2. Thread B tries to update `data[1]`, but it finds that the cache line containing `data[1]` is marked as modified in Core A's cache.
3. The cache coherence protocol forces Core A to write back the ==modified== cache line to main memory and invalidate its copy.
4. Core B can now load the updated cache line into its cache, modify `data[1]`, and mark the cache line as modified in its cache.
5. If Thread A tries to update `data[0]` again, the same process repeats, causing the cache line to bounce back and forth between the two cores.

## Coherence

Coherency and Consistency - what is the difference?

## Synchronization



Architecture of CPU and GPU. What components are removed and what components are added?



# MPI and OpenMP

## Function of MPI

1. **int MPI_Comm_split(MPI_Comm comm, int color, int key, MPI_Comm *newcomm);**

   This function is used to create new communicators by dividing an existing communicator into subgroups based on specified color and key values. `comm` is ths existing communicator that I want to split. `color` is an integer that defines which represent the group label. `key` is the order of the current process in the new communicator. Note that this function is a collective call and ==should be called by all process== with (different) color and key values.

2. What will happen if MPI program uses multiple processes instructions on a single processor machine?

   Numer of processes does not depend on the number of processors of a machine. A single processor machine can also run multiple processes. Different processes communicate with each other by MPI calls instead of global variables. 

3. **int MPI_Bcast(void *buffer, int count, MPI_Datatype datatype, int root, MPI_Comm comm);**

   It is a collective MPI call should be called by all processes within a communicator. `count` is the number of elements to be sent or received, the specified datatype will tell how much is the address offset. `root` the rank of the root processes.

4. int **MPI_Isend**(const void *buf, int count, MPI_Datatype datatype, int dest, int tag, MPI_Comm comm, MPI_Request *request);

   To complete the non-blocking send operation you should use MPI_Waith() or MPI_Test() functions to check the status of the MPI. MPI_Wait is a blocking operation and MPI_Test is not.

5. **MPI_Scatter and MPI_Scatterv**

   Difference compares with broadcast. Broadcast sends the same data to all processes. However, scatter distribute difference blocks of data to all processes. 

   int MPI_Scatter(const void *sendbuf, int sendcount, MPI_Datatype sendtype, void *recvbuf, int recvcount, MPI_Datatype recvtype, int root, MPI_Comm comm);

   int MPI_Scatterv(const void *sendbuf, const int *sendcounts, const int *displs, MPI_Datatype sendtype, void *recvbuf, int recvcount, MPI_Datatype recvtype, int root, MPI_Comm comm);

   displs is the starting index of the sendbuf for the current process

6. **Gather and Allgather**

   int MPI_Gather(const void *sendbuf, int sendcount, MPI_Datatype sendtype, void *recvbuf, int recvcount, MPI_Datatype recvtype, int root, MPI_Comm comm); ==Gather collects data from all processes and store it in the root.==

   int MPI_Allgather(const void *sendbuf, int sendcount, MPI_Datatype sendtype, void *recvbuf, int recvcount, MPI_Datatype recvtype, MPI_Comm comm); ==Allgather collects data from all processes and store in all processes.==

7. **MPI_Barrier** is a collective synchronization function that blocks the calling process until all processes in a specified communicator have called it.

8. **MPI_Alltoall**: each process will have a local variable to send and a local variable to receive. The send data is cut evenly into k=size pieces and be sent to every process including itself. 

   Specifically, this is a good function for matrix transpose. Each processe holds local row and local column variables. Local row is cut evenly and send to every process. Local column receives one piece of data from every process.

## OpenMp Instruction

1. \#pragma omp parallel for: tell compiler to create multiple threads and parallelize the for loop.
2. \#pragma omp parallel for reduction (+:a[i])
3. \# pragma omp parallel for **collapse** (2) reduction (+:a): collapse make the nested loop into the single one. The interation index of the loop should be independent with each other.

4. Schedule clause:
   1. static: \#pragma omp parallel for schedule(static, chunk_size). It divides iteration into equal-sized trunk. Each thread receive predetermined number of iteration to work.
   2. dynamic: #pragma omp parallel for schedule(dynamic, chunk_size). It better deals with the load balance.
   3. guided: #pragma omp parallel for schedule(guided, min_chunk_size). It's similar to dynamic. However, the trunk size starts large and decreases over time to min_chunk_size. Balance the load balancing and overhead.

# Matrix

## Compressed Sparse Row

Use three arrays to represent a sparse matrix. The first array stores the values of non-zero values of the matrix. The second array stores the column index of the corresponding non-zero values. The third array stores an object. Its pointer points to the first non-zero values of each row. Its value is the index of that value in the first array.
