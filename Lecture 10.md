# OpenMP Code Examples

<img src="Figures/non-deterministic.png" style="zoom: 20%;" />



**Why above code has non-deterministic output?**

There are cases when the thread 1 has not finish the update but other threads reach the print function. It's called "race condition".

**How to solve this problem?**

Need to syncronize before print function. 

<img src="Figures/sync.png" alt="image-20230219121122592" style="zoom:50%;" />

Other example of race condition:

```c
#include <omp.h>
int main() {
  int i = 0; // shared variable
  {
    int x; // a local variable for each thread
    x = omp_get_tread_num();
    i++;
    printf("i = %d thread_id %d/n", i, x);
  }
}
```

In the above code, the variable `i` is not atomic because it is being incremented inside a parallel region, without any synchronization mechanism to pretect it from race conditions. Each thread executing the parallel region will have its own private copy of the variable `x` but they all access the shared variable `i`.

```c
#include <omp.h>
int main() {
  int i = 0; // shared variable
  {
    int x; // a local variable for each thread
    x = omp_get_tread_num();
    #pragma omp atomic
    i++;
    printf("i = %d thread_id %d/n", i, x);
  }
}
```

#pragma omp atomic makes sure only one thread at a time can access and modify the shared variable, why is it still parallelism?



# Work Sharing

Shared queue

Work sharing tradeoff

Loop Schedule

	- Static
	- Dynamic



Nested Loop

Lunching and destroy threads itself is expensive, we should reduce the number of times of those operations.

Flatten the loop and compute indices inside the loop. What `collapse` in OpenMP does.



Matrix product