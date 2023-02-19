# Directive

A directive in OpenMP is a compiler instruction that is used to speciyf parallel regions of code, as well as how the work within those regions should be divided among multiple threads.

`#pragma omp parallel for reudction(+: sum)`

#pragma omp parallel for is used to parallelize a loop by dividing its iterations among multiple threads. reduction(+: sum) clause is used to specify that the variable `sum` is a reduction variable and its values across all threads should be combined using addition.