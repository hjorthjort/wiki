```sh
time (COMMAND)
```

e.g.

```sh
time (touch wasm.md && make build -j8 && make test -j8)
```

Give program output and timing profile:

```sh
( touch wasm.md && make build -j8 && make test -j8; )  311.08s user 11.75s system 614% cpu 52.498 total
```

- `user`: time spent in user space, e.g., looping over an array
- `system`: time spent in system calls, e.g., calling `fork` and other calls to the kernel.
- `cpu`: average CPU load, I think
- `total`: wall-clock time

