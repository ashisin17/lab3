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
In the `hash_table_v1_add_entry` function, I added TODO
the mutex at __ line. I blocked __ as a critical section, guaraneteeing correctness because all operations are protected ___

### Performance
```shell
TODO how to run and results
```
Version 1 is a little slower/faster than the base version. As TODO

## Second Implementation
In the `hash_table_v2_add_entry` function, I used __ mutexes.
The mutex1 protects __
The mutex2 protects __
The mutex3 protects __
No more locks are required because 

### Performance
```shell
TODO how to run and results
```

TODO more results, speedup measurement, and analysis on v2

## Cleaning up
```shell
TODO how to clean
```