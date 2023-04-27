- Create cartesian topology

- MP_Comm_split
- Communicators for broadcast-reduce

# Molecular Dynamics

```c++
// The first block of the code is responsible for computing the forces between all pairs of atoms
foreach pair of atoms x,y {
  dist = ||x.loc − y.loc||; 
  if (dist < C) {
    direction = (x.loc − y.loc)/dist;
    force = −(x.charge * y.charge)dist^2;
    x.force −= force * direction;
    y.force += force * direction; 
  }
}
// The second block of code is responsible for updating the position of the 
// atoms based on the calculated forces
foreach atom x {
  temp = x.loc;
  x.loc = 2 * temp − x.oldloc + x.force * x.mass;
  x.oldloc = temp; 
}
```

# Parallel Algorithm

**Assumptions**:

- Assume atoms are placed in a 2 dimensional space. Divide the space in cells.
- Cell dimension is significantly large than the cutoff radius. Atoms may interact only with atoms in neighbor cells. (8 cells which surround it)



**The outline of the algorithm is as followings:**

1. Broad cast atoms to neighbor cells.
2. Compute atom-atom interactions
3. Collect back computed forces
4. Update locations
5. Move particles that crossed cell boundaries



The above method is generally called as spacial decomposition, which is really common in parallel computing. It's important to note that the communication between processes should be optimized to minimize the **communication overhead.**

**What is communication overhead?**

Communication overhead refers to the time and resources consumed by a parallel program itself to exchange data and synchronize between different processes.



## Data Structure for Atoms

We don't care the order of atoms. However, we need to inser and delete atoms in the list when as they move from cell to cell.

Linked list: easy to insert or delete elements. However, it does not utilize cache locality because elements are not stored in a continuous block of memory.

contiguous array: good cache utilization. However,  the array needs to be resized significantly as we insert or delete the elements.

array with "holes": better cache utilization as elements are stored in a continuous block of memory. It's also more efficient to update elements because it could use "holes" to make deleted element and reused later.

## Create Cartesian Topology

**MP_Cart_create:** The function is used to create a virtual Cartesian topology (grid) for processes, enabling them to communicate more efficiently with their neighbors in the grid. We use this function when our data could be matched into a Cartesian grid.

**MPI_Dims_create(size, 2, dims)** : this function is used to calculate the appropriate dimension to for creating a cartesian grid of the processes. 

**MPI_Cart_coords(torus, myrank, 2, mycoords)**: this function is used to convert the rank into 2-dimensional coordinates.