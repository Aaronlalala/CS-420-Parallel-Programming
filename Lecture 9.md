- #pragma omp parallel
- #pragma omp critical
- #pragma omp atomic



**Always true:** local operations appear to local thread to execute in program order. In other words, instructions within a thread scope can only be executed in program order.

Mutual exclusion: however, the order of threads is unknown; non-deterministic.

Worry about coherence: for single data element.

Sequential consistency: 

```c++
// thread 0
x = 1;
print(y);

// thread 1
y = 1;
print(x);

// output: 01 or 11. 10 can never happen.
// Why 00 could happen? It depends on hardware structure. Does that mean sequantial consistency is 
// violated?
// Buffer?

```

It's important to know the difference between consistency and coherence.

