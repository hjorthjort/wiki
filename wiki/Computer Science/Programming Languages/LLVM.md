You can imagine you are coding in a virtual register machine with unbounded number of registers.
Uses *typed three address code*, and static single assignment: every variable is assigned to once in the source (statically).

The language reference:

<https://llvm.org/docs/LangRef.html>

Files
=====

- `.ll` is the textual format
- `.bc` is the byte code format

Most tools can handle both.

The command line tools
======================
Linking and compiling
---------------------

```sh
llvm-link textfile1.ll bytcodefile1.bc -o out.bc
llc --filetype=obj out.bc  # Creates out.bc.a
gcc out.bc.a

llc out.bc  # Creates out.s
gcc out.s
```

`llc` (the compiler) can generate many formats, e.g. `.s` files of x86 code.

Interpreter
-----------

```sh
lli
```

Assmebler
------------

```sh
llvm-dis < [FILE].bc
```

```sh
llvm-as < [FILE].ll
```

Optimizer
---------

```sh
opt [OPTIONS] [INFILE].ll > [OUTFILE].bc
```

Example options: `-mem2reg`, `-O3`.

Use `--time-passes` to see all passes and some stats.

To see optimizations:


```sh
opt -mem2reg sum.ll | llvm-dis
```

Language
========

Blocks
------

Labeled blocks, a sequence of operations that **must** end in a terminating instruction (jump, break, etc.)

