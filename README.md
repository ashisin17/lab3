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
In the `hash_table_v1_add_entry` function, I added the mutex at 88 (lock) and 106 (unlock). The race condition that led to some entries not being properly inserted was because in the case the scheduler runs them in parallel, there's dissonance between the next and head pointer of the insertions based on the specified order. To ensure that only 1 thread executes the insertion: finds the head, checks if it exists and creates a nodes or updates based on the condition, I put a lock around the function so only 1 thread does the insertion into the hash table AT A TIME. 
Besides that, also added 

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
Version 1 is a little slower than the base version because of the additional overhead introduced in the code due to add_entry being essentially serialized. When you force only 1 thread to add an entry at a time, it prevents the use of concurrency for that code segment. Since adding entries to the hash table is a FREQUENT operation, and previously, many threads were able to add the entries concurrently, taking this away means the slowing down of the program! The add entry function now serves as a bottleneck curtailing the performance of the program, while maintaining correctness!

## Second Implementation
In the `hash_table_v2_add_entry` function, I used NUM_MUTEXES mutexes (initially 64, but experimenting with 4096). This is a fine-grained strategy for locking to curtail contention and improve concurrency when multiple threads attempt to add entries. When trying to insert, calcualte an index based on the NUM_MUTEXES. Then, lock the mutex at that specific bucket in the hash table. Thus, serialize access to that buck so only 1 insertion at a time! This is important, because if trying to insert in different buckets, overriding is not a concern. 

### Performance
```shell
TODO how to run and results
cs111@cs111 ~/lab3 (main %) » ./hash-table-tester -t 3 -s 500000
Generation: 129,791 usec
Hash table base: 42,268,091 usec
  - 0 missing
Hash table v1: 67,273,315 usec
  - 0 missing
Hash table v2: 29,076,248 usec
  - 0 missing

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