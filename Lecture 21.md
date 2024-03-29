# Using MPI with OpenMP

## The Difference between MPI and OpenMP

With MPI, each process has its own memory space and executes independently from the other processes (that's the reason we need to pass message). With OpenMP, threads share the same memory. They might have a individual cache. Generally, they also have a shared level cache and they also share the main memory.

MPI deals process-level parallelism and OpenMP deals thread-level parallelism. Threads are entities within a process.



## Initialization

MPI_Init_thread(required, &provided);

required is the level of thread support that will be used in the following computation.

1. MPI_THREAD_SINGLE: in this level, only one thread will execute the entire MPI program. This is essentially the same as using MPI along without OpenMP.
2. MPI_THREAD_FUNNELED: in this level the program can have multiple threads but only the main thread can make MPI calls. Other threads can perform computations only. One thread is responsible for managing MPI communication, and other threads perform computation. Since threads share the memory, it works well generally.
3. MPI_THREAD_SERIALIZED: allow multiple threads to make MPI calls. But only one at a time will make calls.
4. MPI_THREAD_MULTIPLE: is the highest level of thread support. Multiple threads can make MPI calls without restrictions.



## One-sided Communication

It's an MPI feature that allows one process to access the memory of another process directly, without the need to explictly pass message through send and receive operations. This can be beneficial where you have well-defined communication patterns or when one one side knows the location of the other side.

1. Put: A process writes data into the memory of a remote process
2. Get: A process reads data from the memory of a remote process
3. Acumulate: A process performs a reduction operation with its loca data and the data in the memeory of a remote process.

The above operations are non-blocking. Synchronization is required using either fence or lock. They are more efficient because they eliminates the MPI runtime to match send and receive calls.