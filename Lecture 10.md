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



# Parallelizing Graph Traversal using OpenMP Tasks

**How is the graph stored in memory?** 

We could use adjacency matrix or adjacency list to store it.

**An intuitive way to solve problem:**

We could maintain a queue for breadth first search

**How do I parallelize BFS?**

First we construct tasks: #pragma omp task {...} will start a task that can execute on any of the available threads; the calling task may contintue executing in parallel with the newly created task. Code within the clause are executed parallely.

**More specifically, what can I do?**

Whenever I discover a new vertex, spawn a task and assign the task to visit that vertex.

```c
typedef struct {
  int visited; 
  int numneighbors;
  int neighbors[];
} Node;
Node* graph[N];

void visit(int i) {
  int j, k, mark;
  for (j = 0; j < graph[i]->numneighbors; j++) {
    k = graph[i]->neighbors[j];
    #pragma omp atomic
    mark = graph[k]->visited++;
    if (mark == 0) {
      #pragma omp task
      visit(k);
    }
  }
}

int main() {
  #pragma omp parallel;
  #pragma omp single
  visit(0);
}
```

