# Branch

## Branch Structure

In computer programming, a brach structure is a control flow that allows a program to change the order of execution of instructions based on a particular condition.

## Branch Instructions

These are used to implement branching operations such as if-else statements, switch statements, and loops in high-level programming languages.

The target location of the brach instruction is determined by an address or a label. The outcome of the condition depends on registers set by the previous instruction.

## Branch Hazard

A branch hazard is a situation in which the execution of a branch instruction in a computer's pipeline is delayed due to a **data dependency**.

Possible solutions to branch hazard: branch prediction, and dynamic scheduling.

# Pipeline Bubble (empty stage in pipeline)

Pipeline bubble is also known as pipeline stall. It is caused due to the pipeline is not able to execute instructions when waiting for data or resources to become available.

A pipeline bubble is a general term used to describe any type of delay in the pipeline, while a branch hazard refers to a delay caused by the execution of a branch instruction.

## Data Dependency

One of the most common reason for pipeline bubble is a data dependency between instructions. For example, if an instruction is waiting for data from a memory location, the pipeline will be unable to execute the next instruction.

## Dependency Distance

Dependency distance is a measure of the number of instructions between two dependent instructions in a pipeline. ==The greater the distance, the more parallelism is possible==, since more instructions can be executed in between the two dependent instructions.

a[i] = a[i-1] * b: Distance = 1, implies that only one stage in the pipeline is used.

a[i] = a[i-2] * b: Distance = 2, implies that two stages in the pipeline is used.

```c++
// Loop dependence, false dependenc
for (int i = 2; i < n; i++) {
  a[i] = s * a[i-2];
}

// Loop unrolling:
// More efficient code: fewer branches; good if n is large; need extra code to handle
for (int i = 2; i < n; i+=2) {
  a[i] = s * a[i-2];
  a[i+1] = s * a[i-1];
}
if (n%2 == 0) a[n-1] = s*a[n-3];
```

# Examples of False Dependency

## Anti dependence: write after read

```c++
// Update current iteration value using future values
for (int i = 0; i < n; i++) {
  a[i] = s * a[i+1];
}
// However, it's not true dependency. It could be avoided using a temporary variables.
// Compiler will use registers as temporary variables.
for (int i = 0; i < n; i+=2) {
  int t1 = a[i+1];
  int t2 = a[i+2];
  a[i] = s * t1;
  a[i+2] = s * t2;
}
```

## Register Renaming

```
1. ldr 1r , [#1024]
2. add r1 , r1 , #2
3. str r1 , [#148]
4. ldr r1 , [#2090]
5. add r1 , r1 , #4
6. str r1 [#3000]
// above operations have false dependency on register r1. Compiler can solve this by using another register

1.ldr r1 ,[#1024]
2.add r1 , r1 , #2
3.str r1 , [#148]
4.ldr r2 , [#2090]
5.add r2 , r2 , #4
6.str r2 [[#3000]
// 4-6 can execute concurrently with 1-3. However, number of registers are small (e.g. 16).
// If code become more complicated, might runs out of registers.
```

# Control Dependence

```c++
for (int i = 0; i < n; i++) {
  if (condition) {
    // some operations
    break;
  }
}
// cannot execute i = 5 before executing i=4, because it may break earlier.
// However, there is a mechanism called branch prediction. Computer might select the most likely branch.
// If prediction is false, then roll back.
```

# Loop

## Loop Fusion

```c++
for (int i=0;i<N;i++) {
  a[i]=a[i]+b[i];
}  
for(int i=0;i<N;i++) {
  b[i]=b[i]*s;
}

// can merge two loops into one
// It reduces number of branches; reduce number of loads. In each iteration, b[i] only be loaded once.
for (int i=0; i<N; i++) {
  a[i] = a[i] + b[i];
  b[i] = b[i] * s;
}
```

## Avoid Memory Accesses

```c++
int i;
vector<int> a(N);
a[0] = 1;
for (int i = 1; i < N; i++) {
  a[i] = a[i-1] + 1;
}

// The above piece of code could be improved by the following
int temp = a[0];
for (int i = 1; i < N; i++) {
  // Use a temprorary variable to reduce the number of memory accesses.
  temp = temp + 1;
  a[i] = temp;
}
```



