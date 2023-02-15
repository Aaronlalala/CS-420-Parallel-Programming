# Cache Performance

Performance model formula:
$$
f(\beta) = \frac{T}{(1-\beta)T + \beta\tau} = \frac{T/\tau}{(1-\beta)(T/\tau) + \beta}
$$
T is the memory access time. $\tau$ is access time. $\beta$ is cache hit ratio. The nominator is the access time without cache. The denominator is the access time of using cache.

Increase cache-hit ratio will increase the performance of cache.

## Temporal Locality

Temporal locality and spatial locality are two concepts in computer architecture that describe patterns of memory access. 

Temporal locality refers to that a memory location that was recently accessed is likely to be accessed again in the near future. This property is used in caching to increase performance by taking advantage of this predictable behavior.

**Design decision:** Whenever we bring some data to service a CPU request, since it might be accessed again, store it in cache.

## Spatial Locality

Spatial locality refers to the property that memory locations that are near each other in memory are likely to be accessed together. 

**Design decision:** Whenever we access the main memory to fetch address X for a CPU request, we fetch a chunk of data containing X.

They are trying to optimize the performance of caches by predicting the patterns in memory access.

## Jacobi Algorithm

![Jacobi Algorithm](/Users/aaronlalala/Documents/Courses/UIUC/CS 420/Figures/Jacobi Algorithm.png)

It satisfies spatial locality because we are traverse the matrix in row direction. However, it does not promise temporal locality. For example, we are traverse i-1, i, i+1 rows at iteration k, then in next iteration k+1 we will traverse i, i+1, i+2 rows. If the length of matrix is too large. Then when we traverse in the k+1 iteration, the prefetched values of i, i+1 rows might be alrealy overwritten.

To overcome this situation, we could cut the matrix in column direction, and traverse by the following order. 

![Jacobi Divide](/Users/aaronlalala/Documents/Courses/UIUC/CS 420/Jacobi Divide.png)