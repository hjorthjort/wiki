Declaring types
===============

```ocaml
let x : int = 5
```

Functions types can be declared in two ways.

```ocaml
let f : int -> int = fun x -> x + 5 ;;
let f (x : int) : int = x + 5 ;;
let f 
```

Parameterized types
===================

Parameterized types are given with parameters first.

```ocaml
# [1] ;;
- : int list = [1]```
# [a] ;;
- : 'a list = [1]```
```

Jane Street
===========

A very cool company (apparently) that work only in OCaml with High Frequency
Trading. Apparently every functional programmers dream. They are founding a lot
of FP research.

`core` library
==============

They built their own standard library for OCaml. It's great.

The docs: <https://ocaml.janestreet.com/ocaml-core/latest/doc/core>
