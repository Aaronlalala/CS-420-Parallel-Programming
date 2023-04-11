1. Pipeline parallelism. Could see the example from review lecture question 1.
   1. Pipeline speed up is relate to pipeline depth.
2. Memory Structures
   1. Register: access time = 1 cycle
   2. Mutiple level cache: number of cycles take to access the first level of cache is 1-2 cycles.
      1. Level 2 cache or last layer of cache access time: 20-30 cycles.
   3. Main memory
      1. Will everything be hit in cache? No, there are cache misses?
      2. Will everything be hit in main memory? No, some data are stored in secondary storage.
3. Cache property (Example question 2)
   1. Temporal Locality
   2. Spacial Locality
4. Four types of cache misses
   1. Cold miss
   2. Complicate miss
5. Fully associative cache and indirective cache

![image-20230309014435588](../../../../Library/Application Support/typora-user-images/image-20230309014435588.png)

6. What is reduction, atomic and critical in open mp? What are the differences?

When use dynamic and static in omp parallel for schedule?

When work in iterations are uneven, dynamic schedule is more efficient than static. 