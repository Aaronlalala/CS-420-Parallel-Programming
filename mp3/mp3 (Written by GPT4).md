```c
void send_recv_ghosts(int tag, int w, int h, float tile[w + 2][h + 2], float** send_buffers, float** recv_buffers) {
    MPI_Request reqs[8]; // 4 MPI_Isend and 4 MPI_Irecv requests
    int req_count = 0;

    // Directions: 0 - left, 1 - right, 2 - up, 3 - down
    int neighbors[4][2] = {
        {kGridX - 1, kGridY},
        {kGridX + 1, kGridY},
        {kGridX, kGridY - 1},
        {kGridX, kGridY + 1}
    };

    for (int i = 0; i < 4; i++) {
        int nx = neighbors[i][0];
        int ny = neighbors[i][1];

        // Check if the neighbor is within the process grid
        if (nx >= 0 && nx < kGridCols && ny >= 0 && ny < kGridRows) {
            int neighbor_rank = ny * kGridCols + nx;

            // Copy the ghost cells to the send buffer
            if (i < 2) { // Left or right
                int col_no = (i == 0) ? 1 : w;
                copy_colbuf_out(w, h, tile, col_no, send_buffers[i]);
            } else { // Up or down
                int row_no = (i == 2) ? 1 : h;
                copy_rowbuf_out(w, h, tile, row_no, send_buffers[i]);
            }

            // Send the ghost cells using MPI_Isend
            MPI_Isend(send_buffers[i], (i < 2) ? h : w, MPI_FLOAT, neighbor_rank, tag, MPI_COMM_WORLD, &reqs[req_count]);
            req_count++;

            // Receive ghost cells using MPI_Irecv
            MPI_Irecv(recv_buffers[i], (i < 2) ? h : w, MPI_FLOAT, neighbor_rank, tag, MPI_COMM_WORLD, &reqs[req_count]);
            req_count++;
        }
    }

    // Wait for all MPI_Isend and MPI_Irecv requests to complete
    MPI_Waitall(req_count, reqs, MPI_STATUSES_IGNORE);

    // Copy the received ghost cells from recv_buffers to the tile
    for (int i = 0; i < 4; i++) {
        int nx = neighbors[i][0];
        int ny = neighbors[i][1];

        if (nx >= 0 && nx < kGridCols && ny >= 0 && ny < kGridRows) {
            if (i < 2) { // Left or right
                int col_no = (i == 0) ? 0 : w + 1;
                copy_colbuf_in(w, h, tile, col_no, recv_buffers[i]);
            } else { // Up or down
                int row_no = (i == 2) ? 0 : h + 1;
                copy_rowbuf_in(w, h, tile, row_no, recv_buffers[i]);
            }
        }
    }
}

```

