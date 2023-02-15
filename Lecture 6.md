# Vector Operations

Assembly instruction: vector operations are a type of assembly instruction. 

**Benefits of assembly instruction:**

- Reduce for loop (branch condition)
- Reduce number of instruction
- Parallelism
- No need to consider dependency of data

Vector register?

How does one use vector instructions?

- Trust the compiler to do it
- Write assembly code
- Use OpenMP



```c++
# pragma omp simd
for (int i =0; i < 8; i++) {
	a[i] = a[i] + b[i];d
 }
// #pragma omp simd is a directive in the OpenMP programming model. 
// It informs the csompiler that the loop that follows should be executed in a way that takes
// advantage of Single Instruction Multiple Data (SIMD) vectorization, if possible.
```

## Programmer Intervention

```c++
void test(float* A, float* B, float* C) {
  for (int i = 0; i < N; i++) {
  	A[i] = B[i] + C[i];
  }
}
// Declared an array, but the array does not store actual values but the addresses where values
// stored.
// In this case, the compiler will not vectorize the loop by default, because it does not know
// whether there exist data dependency. Because there is the case when float* a = &b[1].

void test(float* __restrict__ A, float* __restrict__ B, float* __restrict__ C) {
  for (int i = 0; i < N; i++) {
  	A[i] = B[i] + C[i];
  }
}
```

## Data Alignment

Want to make sure is align at the boundary of cache block for vector instructions

```c++
struct st {
  char A;
  // constructor will create some dummy data after memory of A to make usure
  // Array B is aligned.
  int B[64] __attribute__ ((aligned(16)));
  float C;
  int D[64] __attribute__ ((alinged(16)));
}
```

## What is the difference between a program and a process?

A program is a set of instructions that can be executed by a computer to perform a specific task. 

A process is a running instance of a program, and it has its own memory space, system resources, and execution state. A single program can have multiple processes running simultaneously, each with its own execution state.

The process might have some big virtual memory, but only small of the actual memory is mapped to the main memory. When process is activated, the data will be fetch from main memory to cache, then to register.