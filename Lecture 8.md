`Page`: n computing, a page refers to a unit of memory management in the operating system. In the context of secondary storage, a page usually refers to a fixed-size block of data.



# Process

Process is a concept related to computer multitasking and refer to the operating system's management of multiple tasks running simultaneously.

A process ia a self-contained execution environment, including memory, files and network connections, that are allocated to the running program. Each process has its own memory (virtual), part of it will be mapped to the main memory (RAM). 

**Does different processes share the same cache?**

No. Each process runs independently and has its own memory space. Different processes cannot interfere with each other's memory. Cache is a type of high-speed memory that is used to store frequently-accesed data. It is usually implemented at the processor or CPU and is managed by the operating system. Operating system allocates a separat cache for each process.

**Super Scalar Process**



# Thread

A thread, is a lightweight "sub-process" that is executed within a process. Threads share the same memory space as the process they belong to and run concurrently with other threads within the same process. Communication between threads is easier than communication between processes.

The `execution unit` (physical processing unit in a computer such as CPU core or an execution pipeline with a CPU core that can execute instructions) is not necessarily shared. It depends on the architecture design.

Multiple threads in the same process can share the same cache (more oftenly they share Last Level Cache). Sometimes, it can increase the performance, because different threads may access similar data and it reduces the number of times of accessing main memory individually. Overall, it depends on the specific situation.



# Core

It refers to a physical processor unit within a CPU. It is the basic processing unit that executes instructions. In a multi-core processor, there are multiple cores within the same CPU. Each core is capable of executing instuctions independelnty for parallel processing. `Execution unit` which mentioned earlier is actually hardware resource within a core. An execution unit typically includes a set of register, arithmetic logic unit and some other functional units.



# LLC

![image-20230209224340783](/Users/aaronlalala/Library/Application Support/typora-user-images/image-20230209224340783.png)

After thread 0 modify the value of x, the x in LLC is not updated and t1 will fetch x in LLC. How to deal with this situation?

**The question is when should the data being updated in LLC?**

**Write-through policy:** every time thread 0 update the data in the cache, it immediately written back to the shared LLC, ensuring that all caches ahve access to the latest data. However, it reduces the performance.

**Write-back policy:** updates to data in the cache are not immediately written back to shared LLC, but instead kept in the cache until it is replaced by new data.

Another solution is avoid private level 1 cache. Multiple threads share the same cache. However, in this case, if one of the thread is heavily relied on memory, it will occupied all the cache space. And other threads might not have enough cache space.



# Cache Cohorencey Protocals

In previous mentioned situation,  one such protocol that can be used is the MESI (Modified, Exclusive, Shared, Invalid) protocol.

When a processor wants to access a cache line in the LLC, it first checks its private cache. If the cache line is not found in the private cache, the processor sends a request to the LLC. If the cache line is found in the LLC and is in the Exclusive or Shared state, the processor can access it. If the cache line is in the Modified state, the processor must first obtain exclusive access to the cache line by invalidating all other copies of the cache line in other private caches.(cause concorency miss)

1. Thread_0 and thread_1 fetch the copy of x to their private caches.
2. Thread_0 updates the value of x, all copies of x in other private caches will be invalidated.
3. When thread_1 wants to conduct some operations on x, it will call LLC to fetch x from the private cache. Then fetch the updated value from LLC. 

 For example, in above situation, when t0 update the value of x, it should invalidate copy of x in t1's private caches.



States are applied to cache line, not the entrie cache.

Shared State: when thread accesses values in LLC, it's in the shared state.

Invalid State: when thread tries to update a value, it's in the invalid state.

Other protocols: snooping protocol, directory protocol... When number of cores are large, directory is better.
