Code
====
Here's the documentation: <https://futhark.readthedocs.io/en/latest/>.
Here's a good entry point for functional programmers: <https://futhark.readthedocs.io/en/latest/versus-other-languages.html>

Important built-in functions
----------------------------

Full list: <https://futhark-lang.org/docs/doc/futlib/soacs.html>

There are some real workhorses of Futhark. The compiler will use it's knowledge to optimize these agressively.
Some examples:
* `map`, `map2`, `map3`
* `reduce`, which requires the operator to be associative (it's *not* a fold)
  - also: `reduce_comm` for commutative operators, which allows furhter optimization. The compiler knows that built-ins such as `(+)` is commutative and will optimize when calling `reduce (+)`, but if we supply our own operator which we promise is commutative, we can call `reduce_comm` for better performance.
* `scan`
* `scatter`
* `reduce_log_index`

Comments
--------

Haskell style:

```futhark
-- This is a comment.
let foo : i8 = 42
```

Tools
=====

Generate input data
-------------------

Generate two arrays of 100 000 randomd floats in the interval [0,2].
Add the `-b` flag to get the efficient binary format instead of text format.

```sh
futhark dataset --f32-bounds='0:2' -g [100000]f32 -g [100000]f32 > data100000.in
```

Benchmark
---------

Make some datasets with `futhark dataset` and add as a prelude to the `.fut` file:

```futhark
-- ==
-- input @ pi100.in
-- input @ pi10000.in
-- input @ pi1000000.in
```

Note that it must be exactly this format, with `-- ==` in the beginning and `input @ filename`.

Then:

```sh
futhark bench --backend=[c|opencl] --json [OUTPUTFILE] [SOURCE_CODE_FILE]
```

The source code file is a `.fut`.
The JSON file contains run data.

It is also possible to do benchmarking without first generating input data in separate files (just specifying what kind of input is desired), and to specify the expected result of a run (to ensure correctness).
The following would give the input 100 (the integer) to the main function, and check that the output is 4950.

```futhark
-- input { 100 }
-- output { 4950 }
```

About
=====

Futhark is a great choice for performing massively parallel GPU computations. It is a domain-specific language for array manipulation and number crunching. It is *not* general purpose, but is easy to compile to a Python module, or to C (which can be compiled to an object filed which can be called via an FFI from most mainstream languages.)

Philosphy
---------

Tagline: "Gotta go fast!" Futhark is built to be fast. That means the only features that get added are those which the developers of the Futhark compiler can figure out how to make fast on a GPU. That means the language is extended gradually

Futhark does not support allow you to do things you might reasonably expect to be allowed. For instance, no irregular arrays: if you have an array of arrays, it must be a 2D-matrix (no ragged rows).
