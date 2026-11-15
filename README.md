# Memory Allocator

A drop-in replacement for `malloc`, `free`, `calloc` and `realloc`, written in C. Built February to June 2026.

**Status:** 🚧 Work in progress. The source isn't published here yet.

## Why

I had been calling `malloc` for two years without knowing what it did with the 24 bytes I asked for, or why a program that frees everything can still keep growing.

## What I did

The first version was a single free list, first fit, growing the heap with `sbrk`. It worked, and it was slow: every `free` walked the whole list. So I split free blocks into 12 size classes (16 B to 32 KiB, doubling), added boundary tags so a freed block merges with both neighbours in constant time, and sent anything over 128 KiB straight to `mmap`. Threads came last, with one arena per thread and a lock each.

That threaded version deadlocked the first time a program called `fork`. The child inherited an arena whose lock belonged to a thread that no longer existed. `pthread_atfork` handlers that take every arena lock before the fork and release them after fixed it.

## Result

- Runs `ls`, `git status` and `python3` through `LD_PRELOAD` with Valgrind reporting nothing.
- On a benchmark of a million random allocations between 16 bytes and 4 KiB, it's about 9% slower than glibc, and its peak heap is 18% bigger.
- About 1,400 lines of C, 86 tests.

## Tools

C on `sbrk`, `mmap` and pthreads. Debugged with GDB and Valgrind, built with Make.

More at [ernestbaffour.com](https://ernestbaffour.com/#allocator).
