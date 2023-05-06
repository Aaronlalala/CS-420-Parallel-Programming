# GPU Programming

# Arithmetic Logic Unit

ALU can perform some arithmetic operations addition, subtractionm multiplicaiton and division. ALso the logical operations AND, OR, NOT, XOR. It's a fundamental component in CPU and GPU.

In GPU, ALUs are organized intro a large number of parallel processing units.



## Brach divergent

It refers to a situation where threads within a group (executed simultaneously by a GPU) follow differenct execution paths due to conditional statements. GPU must allocate extra resources for both execution paths (if and else).