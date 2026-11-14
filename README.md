# Custom Memory Allocator

A drop-in heap allocator implementing `malloc`, `free`, `calloc`, and `realloc`, built on `sbrk` and `mmap`.

**Status:** 🚧 Work in progress

## Highlights

- Segregated free lists with block splitting and coalescing to reduce fragmentation
- Large allocations served directly with `mmap`
- Thread-safe with per-thread arenas to avoid lock contention
- Tested against real programs via `LD_PRELOAD`, verified with Valgrind

## Tech

C, sbrk/mmap, pthreads, Valgrind, GDB, Make
