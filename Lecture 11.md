# Compresed Row Storage



# Work Sharing Mechanisms

In OpenMP work sharing, reduction, atomic, and critical are three different mechanism that can be used to ensure correct results when multiple threads are execting a parallel region simultaneously.

## Reduction

Reduction is to combine the results of each thread's computation into a single, final result. This is typically used when each thread computes a partial results, and these partial results need to be combine to obtain the overall results. For example, we are summing our an array and each thread computes part of the total result.

```c
#pragma omp parallel for reduction(+:sum)
for (int i = 0; i < n; i++) {
  sum += array[i];
}
```

## Atomic

Amotic operations ensure that a single memory location is accessed and modified by only one thread at a time. This is typically used when multiple threads need to update a shared variable.

```c
#pragma omp parallel for
for (int i = 0; i < n; i++) {
  #pragma omp atomic
  sum += array[i]
}
```

## Critical

Critical sections are ensure that only one thread at a time can execute a particular section of code.  This is typically used when multiple threads need to access a shared resource that cannot be accessed concurrently.

```c
#pragma omp parallel for
for (int i = 0; i < n; i++) {
  #pragma omp critical
  {
    sum += array[i]
  }
}
```





# Dynamic, Static and Guide

Dense matrix use static. If matrix is sparse, we have choice to use static. 

What is load balancing?

## Static Scheduling

```c
#pragma omp parallel for schedule(static, chunk)
```

Loop iterations are divided into a fixed size blocks with size chunk. Each block is assigned to a thread. In static scheduling, the next block of iterations is not assigned to the thread that first finishes its assigned block. Instead, the blocks are assigned to threads in a predefined order

## Dynamic Scheduling

Loop iterations are divided into smaller chunk, often the size is 1. Each block is assigned to a thread. After that, every block is assigned to the thread that finishes first.

## Guided Scheduling



## Gauss-Seidel Computation

Diagonally parallelizing.
