# Hash Hash Hash
The goal of the lab is to make a hash table implementation with linked lists thread safe to allow for concurrency and program optimization. Moreover, the goal is to maintain performance and correctness while exploring different techniques for program optimization with thread concurrency.

## Building
```shell
make clean && make
```

## Running
```shell
./hash-table-tester -t 8 -s 50000
Generation: 97,234 usec
Hash table base: 4,473,810 usec
  - 0 missing
Hash table v1: 1,633,141 usec
  - 8 missing
Hash table v2: 2,022,440 usec
  - 4 missing
```
- t: number of threads
- s: number of hash table entries
Provided the initial run without any modifications made to v1 and v2.

## First Implementation
In the `hash_table_v1_add_entry` function, I added the mutex at 80 (lock) and 98 (unlock). The race condition that led to some entries not being properly inserted was because in the case the scheduler runs them in parallel, there's dissonance between the next and head pointer of the insertions based on the specified order. To ensure that only 1 thread executes the insertion: finds the head, checks if it exists and creates a nodes or updates based on the condition, I put a lock around the function so only 1 thread does the insertion into the hash table AT A TIME.

### Performance
```shell
cs111@cs111 ~/lab3 (main %) » ./hash-table-tester -t 8 -s 50000
Generation: 33,239 usec
Hash table base: 638,716 usec
  - 0 missing
Hash table v1: 1,081,882 usec
  - 0 missing
Hash table v2: 463,243 usec
  - 0 missing

```
Version 1 is a little slower than the base version. When you force only 1 thread to add an entry at a time, it prevents the use of concurrency for that code segment. I essentially serialized the execution of the function. Since adding entries to the hash table is a FREQUENT operation, and previously, many threads were able to add the entries concurrently, taking this away means the slowing down of the program! The add entry function now serves as a bottleneck curtailing the performance of the program, while maintaining correctness!

## Second Implementation
In the `hash_table_v2_add_entry` function, I used __ mutexes.
The mutex1 protects __
The mutex2 protects __
The mutex3 protects __
No more locks are required because 
In the `hash_table_v2_add_entry` function, I added TODO
the mutex at 95 line. I blocked SLIST_INSERT_HEAD(list_head, list_entry, pointers) as a critical section, guaranteeing correctness because all operations are protected, ensuring that the insertion of the new node into the head position of the bucket is atomic and mutually exclusive. 

The add entry function has three basic functions: getting into the index of the hash table, check if it exists (so then updating the value), and if not, inserting the new node into the head position of the bucket. Narrowing down to where the race conditions occur, the order of the insertion when updating the head and the next pointer are the places of conflict. We want the next and head pointers to be updated based on the node being inserted. 

SLIST_INSERT_HEAD was specifically targeted because this is the main operation for inserting the node through updating the pointers.


### Performance
```shell
TODO how to run and results
```

TODO more results, speedup measurement, and analysis on v2
As opposed to v1, v2 narrowed down the critical section to be the SLIST_INSERT_HEAD operation, minimizing the duration of locking and reducing contention. Threads trying to add entries concurrently can proceed without concurrency that is NOT NECESSARY for correctness. Because this is finer-grain locking, threads are less likely to block each other or at least, block each other for shorter periods of time.
As indicated by the times, v2 is faster than v1 due to the finer-grain serialization.
It is faster than base because it prevents any data corruption (entries wont be missed) and reduces contention with the other threads considering that it is a shared hash table. Sincere there is proper synch, race conditions are eliminated leading to improved concurrent access to the struct data structure.

## Cleaning up
```shell
make clean
```
Makefile takes care of this