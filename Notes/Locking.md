# Locking

>  **Locking** is the mechanism used to control simultaneous use of data resources.

When multiple database users are accessing/modifying the same piece of data, how does the database server handles this problem.

When some portion of the database is locked, any other users trying to modify (or read) that data must wait until the lock has been released.

## Two Locking Strategies

1. **Both read and wirte locks.**

   Database writers must request and receive from the server a **write lock** to modify data.

   Database readers must request and receive from the server a **read lock** to query data. While multiple users can read data simultaneously, only one write lock is given out at a time for each table (or portion thereof), and read requests are blocked until the write lock is released.

2. **Only write lock, *versioning* for readers.**

   Database writers must request and receive from the server a write lock to modify data.

   Readers don't need a lock to query data. However, the server ensures that a reader sees a consistent view of the data (the data seems the same even though other users may be making modifications) from the time her query begins until her query has finished. This approach is known as **versioning**.

| Database             | Stategy                                            |
| -------------------- | -------------------------------------------------- |
| Microsoft SQL Server | 1                                                  |
| Oracle Database      | 2                                                  |
| MySQL                | Both, depoending on the choice of *storage engine* |

### Pros and Cons

1. Approach 1 can lead to long wait times with many concurrent read and write requests.

2. Approach 2 can be problematic if there are long-running queries while data is being modified.

   If a read query takes 10 minutes to run, and some data is modified in the first minute, the result of the read query would be out dated.

## Lock Granularities

The server may apply a lock at one of 3 different levels, or **granularities**.

| Granularity | Description                                                  | pros                                | cons                                                        |
| ----------- | ------------------------------------------------------------ | ----------------------------------- | ----------------------------------------------------------- |
| Table Locks | Keep multiple users from modifying data in the same table simultaneously | Little bookkeeping (save resources) | Large wait times as number of users increases               |
| Page Locks  | Keep multiple users from modifying data on the same page (a page is a segment of memory generally in the range of 2 KB to 16 KB) of a table simultaneously |                                     |                                                             |
| Row Locks   | Keep multiple users from modifying the same row in a table simultaneously | More bookkeeping                    | Allows many users to modify the same table (different rows) |

| Database             | Granularity                                                  |
| -------------------- | ------------------------------------------------------------ |
| Microsoft SQL Server | table, page, row locking. *escalate* locks from row to page, and page to table under certain circumstances. |
| Oracle Database      | only row locking, (never *escalate* locks)                   |
| MySQL                | table, page, row locking (depending on choice of storage engine) |



