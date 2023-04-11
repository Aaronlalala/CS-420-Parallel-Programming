# Eager Protocol and Rendezvous Protocol

Eager protocol's goal is to increase the speed of sending messages. Eager protocol let send process sends data to receiver's buffer and does not wait receive protocol to be ready. It's fast and simple to implement. However, the scalability is bad. As size of message increases, it may cause receiver buffer overflow.

Rendezvous protocol designs for large message communication. The sender process sends a request to the receiver, indicating its intent to send a message (thus it needs extra message to communicate). The receiver process checks if it has enough buffer space and if it is ready to receive the message. If so, it sends an acknowledgment back to the sender. Upon receiving the acknowledgment, the sender begins to transfer the message in chunks to the receiver.



# Reduction Operation

A process combines data from multipile parallel processes and apply some operation on them, like addition, multiplication, maximum, minimum, etc. 

MPI_Reduce(&send_data, &recv_data, count, MPI_INT, MPI_SUM, root, MPI_COMM_WORLD);

```c
int local_items; // The number of items processed by this process (5, 8, 3, or 10 depending on the process)
int total_items; // Only relevant for the root process (Process 0) to store the result

// Initialize local_items for each process with their corresponding value (5, 8, 3, or 10)
// send all local_items
// sum them up
// store in process 0's total_items
MPI_Reduce(&local_items, &total_items, 1, MPI_INT, MPI_SUM, 0, MPI_COMM_WORLD);

if (rank == 0) {
    printf("Total items processed: %d\n", total_items);
}
```

MPI_Allreduce: perform the same operation as MPI_Reduce. However, it broadcast the results to all participate processes.



# Jacobi Method Example

```c
double a[2][M][N];
double local_err, global_err;
MPI_Request req[4];
MPI_Comm_rank(MPI_COMM_WORLD, &rank);
MPI_Comm_size(MPI_COMM_WORLD, &size);

// Assume (M-2)*size = N
do {
    // Send/receive data to/from the upper neighboring process
    if (rank > 0) {
        // Send the first row of the local domain (a[l][1][1] to a[l][1][N-2]) to the upper process
        MPI_Isend(&a[l][1][1], N-2, MPI_DOUBLE, rank - 1, 0, MPI_COMM_WORLD, &req[0]);
        // Receive the last row of the upper process's domain (a[0][1] to a[0][N-2]) into the local ghost row
        MPI_Irecv(&a[0][1], N-2, MPI_DOUBLE, rank - 1, 0, MPI_COMM_WORLD, &req[1]);
    }

    // Send/receive data to/from the lower neighboring process
    if (rank < size - 1) {
        // Send the last row of the local domain (a[l][M-1][1] to a[l][M-1][N-2]) to the lower process
        MPI_Isend(&a[l][M-1][1], N-2, MPI_DOUBLE, rank + 1, 0, MPI_COMM_WORLD, &req[2]);
        // Receive the first row of the lower process's domain (a[l][M-1][1] to a[l][M-1][N-2]) into the local ghost row
        MPI_Irecv(&a[l][M-1][1], N-2, MPI_DOUBLE, rank + 1, 0, MPI_COMM_WORLD, &req[3]);
    }

    // Wait for the non-blocking send/receive operations to complete
    if (rank == 0) {
        MPI_Waitall(2, &req[2], MPI_STATUSES_IGNORE);
    } else if (rank == (size - 1)) {
        MPI_Waitall(2, &req[0], MPI_STATUSES_IGNORE);
    } else {
        MPI_Waitall(4, req, MPI_STATUSES_IGNORE);
    }

    // Compute new values and update local_err
    local_err = 0;
    for (i = 1; i < N-1; i++) {
        for (j = 1; j < N-1; j++) {
            a[1-l][i][j] = 0.25 * (a[l][i-1][j] + a[l][i+1][j] + a[l][i][j-1] + a[l][i][j+1]);
            local_err = fmax(local_err, fabs(a[1-l][i][j] - a[l][i][j]));
        }
    }
    l = 1 - l;

    // Find the maximum local_err across all processes and store it in global_err
    MPI_Allreduce(&local_err, &global_err, 1, MPI_DOUBLE, MPI_MAX, MPI_COMM_WORLD);
} while (global_err > maxerr);

```

# Data Distribution

1. MPI_Bcast: it is used to broadcast a message from one process to all other processes in a communicator, including root itself. All processes receive the same data. Used when all processes need a copy of some message.
2. MPI_Scatter: It is used to distribute equal-sized chunks of array from on process (the root). Each process receives a unique portion of the data.
3. MPI_Allgather: is used to gather data from all processes in a communicator and distribute the combined data to all processes.



Alltoallv?

# Other MPI Functions

**MPI_Barrier:** it's also a collective communication operation in MPI that serves as a synchronization mechanism among the processes. It's used to ensure that all processes reach a particular point in the program before any of them can proceed further.