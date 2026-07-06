---
layout: page
title: Thread Pools and Synchronization in Java
permalink: /threadpool
---

This project uses a thread pool to compute the product of multiple matrices by executing a collection of tasks, each of which is assigned to a worker thread in the pool, to produce a final product matrix.

## Description
A single task consists of computing a single cell of a matrix product. With four input matrices, *A*, *B*, *C*, and *D*, I first compute the intermediate matrices *X = AB* and *Y = CD*, then compute the final product matrix *Z = XY*. The input matrices are initialized with random values in the range [-1000, 1000].

The initial goal of this project was to gain more insight and experience using thread pools and synchronizing tasks in a multithreaded environment, but once the initial implementation was complete, most of my effort shifted toward optimization. The remainder of this project description therefore focuses on those optimizations.

## Optimizations
The goal was to compute the final product matrix in under five seconds using four input matrices of size 3,000 x 3,000 (9 million elements each) and eight worker threads. As shown in the [Results](#results) section, this goal was achieved. I implemented the following optimizations that made this possible:

- Stored the matrix data in primitive arrays so that elements are laid out in contiguous memory, improving cache locality and increasing the cache hit rate.

- Since matrix multiplication computes the dot product between the rows of one matrix and the columns of another, I store one matrix in transposed form. This eliminates the overhead of repeatedly accessing individual columns by allowing both operands to be traversed sequentially. To avoid additional overhead, the transposed matrix is constructed as elements are inserted rather than by transposing an already completed matrix.

- Used a primitive array as a circular task buffer for each worker thread. Like the matrices, the circular buffer is stored in contiguous memory to improve cache locality. I initially used a `ConcurrentLinkedQueue`, but although the performance difference was small, the circular buffer consistently produced slightly better average execution times.

- Each worker thread maintains a private circular task buffer rather than requesting individual tasks from the shared scheduler. When its buffer becomes empty, the worker synchronizes with the scheduler to obtain a single `TaskStart` task, which populates the buffer with a new batch of computation tasks. The worker then executes the batch entirely from its local queue before synchronizing again. This reduces the frequency of synchronized scheduler access while allowing queued tasks to remain in contiguous memory, improving cache locality.

## Results
The following results were obtained using a 13th Gen Intel Core i7-13700K processor.

Each input matrix has dimensions of 3,000 x 3,000 (9 million elements each) and the thread pool consists of eight worker threads. The execution time for each complete matrix multiplication is shown, along with a cumulative execution time of **4.681** seconds.

[![matrices](/assets/img/matrix-results.png)](/assets/img/matrix-results.png)