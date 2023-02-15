# Logistics

Homework is writing assignment. MP is coding.

For non-cs students.

Slides will be uploaded to Piazza.

# Registers

A processor register (CPU register) is one of a small set of data holding places that are part of the computer processor. It could hold data like an instruction, a storage address, etc. 



Each CPU or a family of CPUs have their own instruction set. It is the duty of the programmer to understand the instruction set and write code which the CPU can understand or write code in higher level language and rely on the compiler to generate architecture specific code.

# Assembly Operations

Assembly language is a low level programming language which has a strong correspondence between the instructions in the language and the machine code understood by CPU.

```c++
// consider the following example
int sum = 0;
for (int i = 1; i <= N; i++) {
  sum += A[i];
}
```

**Some common instructions:**

**ld means load data:** ld R4, [R3]; load data from memory pointed by R3 and store in R4.

**add means sum of variables:** add R0, R0, R4; sum R0 and R4, then store the result in R0.

**add could also use to update the pointer:** add R3, R3, #4; increment the pointer to point to the data stored in next 4 bytes.

**cmp used to compare two operands:** cmp R1, R2; check whether i == N?

**bne stands for brach if not equal:** bne .loop; 



- Brackets [] are used to indicate memory addresses.
- \# followed by a number indicates immediate values.



# Pipelining

Professor gave an example of construction of houses. 

Assume to construct a house takes 5 days and the process could be divided into 5 stages: foundation, walls, roof, door and windows, with each stage taking 1 day.

Normally, it takes 5N days to construct N houses. We observe that workers responsible for laying the foundation are done with their work after day 1 and wait from day 2 to 5. However, instead of just waiting, we let him construct the foundation for the next house. In this situation, all workers won't wait for others. It only takes N + 4 days to construct N houses.

<img src="/Users/aaronlalala/Documents/Courses/UIUC/CS 420/Figures/Pipelining.png" alt="Pipelining" style="zoom: 50%;" />

We could ask further interesting questions regarding this model. What if it has 10 stages with each stage taking half days?

After pipelining, every half day there is a house constructed. Thus, at the end of day 5, they construct one house. After that, they construct two houses per day. In general, it take $\frac{N-1}{2} + 5$ days to construct N houses.

# Instruction Cycle of CPU

The instruction cycle is also known as fetch-decode-execute cycle. It's the basic process that a CPU follows to execute instructions stored in memory. The cycle consists of the following steps:

1. **Instruction Fetch (IF): **The CPU retrieves the next instruction from memory, using the program counter to locate the address of the instruction.
2. **Instruction Decode (ID):** The CPU decodes the instruction, determining what operation it specifies.
3. **Execute (EX):** The CPU performs the operation specified by the instruction, using data stored in registers of memory.
4. **Memory Access (MEM):** The CPU updates the program counter to point to the next instruction, and any other relevant registers or memory locations.
5. **Writeback (WB):** During this stage, both **single cycle and two cycle instructions** write their results into the register file.

More specifically, it is called classic five stage RISC pipeline.

<img src="/Users/aaronlalala/Documents/Courses/UIUC/CS 420/Figures/Instruction Cycle.png" alt="Instruction Cycle" style="zoom:33%;" />

## Single Cycle and Two-Cycle Instructions

Single cycle instructions are executed in a single CPU cycle. They are completed within on pass through the pipeline. These instructions are typically simple operations such as moving data between registers or basic arithmetic algorithm. Two-cycle instructions are more complex, which require two passes through the pipeline to complete. Like memory access or floating-point operations. ==(Why?)==

## Some questions relate to this Instruction Cycle

**What's the difference of data store in memory or in register?**

Data stored in memory is generally slower to access than data stored in registers. Because registers are part of CPU and memory is separate from CPU. Memory space is much larger than the space of registers. In general, data that is frequently used or needs to be manipulated quickly is stored in registers.

**What is being decode?**

The instruction is typically represented as a binary code. Decode process will interpret it to series of signals that control the operations of CPU.



