General advice
==============

Look for garbage collection bottlenecks.
If you are not getting speedups, set ridiculous large nursery size, more than you'll need.
Otherwise, a good nursery size should (usually) not be larger than L2 cache (256 Kb per core on intel Haswell).

Compiling and running
=====================

[Using Symmetric Multiprocessor](https://downloads.haskell.org/~ghc/7.0.4/docs/html/users_guide/using-smp.html)

In short:

```sh
ghc -O2                  \ # When running for performance, always optimize.
    --threaded           \ # Allow threading.
    --rtsopts            \ # Enable run-time system flags.
    --eventlog           \ # Enable profiling, ThreadScope uses this log.
    --feager-blackholing \ # See below
    MyHaskellProgram.hs
```

Run with

```sh
./MyHaskellProgram +RTS -N[x]
```

where `x` is the number of cores (usually actual cores, not hyperthreads, but do experiment), minus 1 (leave one for the OS).
On my Intel Haswell CPU, it would be

```sh
./MyHaskellProgram +RTS -N3
```


Blackholing
-----------

Blackholing means that when a thread starts evaluating a thunk, it will mark it as in progress.
It's a bit like a lock, except other threads don't have to respect it, it's just a suggestion.
It helps threads avoid doing duplicated work (even though it can happen, if two threads grab the same piece of work at the same time).
The flag `-feager-blackholing` is sensible and usually increases performance.

The tools
=========

ThreadScope
-----------

