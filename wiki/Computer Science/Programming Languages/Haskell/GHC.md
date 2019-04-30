Useful flags
============

- `-H`: flag sets initial heap size; if you never have to grow your heap, you can save some time, so try finding the optimal size right away.
- `-s`: gives stats, good for quick profiling.
   - `INIT` is boot-up time for the runtime system.
   - `MUT` is time spent by the mutator (actual program, not GC).
   - `GC` is time spent in garbage collection.
   - `EXIT` is time spent winding down.
- `-A[SIZE]` sets the nursery size, e.g. `-A100M`.

