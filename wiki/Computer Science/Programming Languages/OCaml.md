Playing around
==============

Make a file, say `Foo.ml`.

```ocaml
module Foo =
  struct
    let myval : int = 10
  end
```

Fire up the REPL (`utop` is best, but `ocaml` is okay).

```ocaml
#use "Foo.ml" ;;
open Foo ;;
Foo.myval ;;
- : int = 10
```

To make changes, reload.

```ocaml
module Foo =
  struct
    let myval : int = 10
    let myfun (a : int) = a + 10
  end
```

```ocaml
#use "Foo.ml" ;;
open Foo ;;
myfun Foo.myval ;;
- : int = 10
```

Basic syntax
============

Declaring types
---------------

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
-------------------

Parameterized types are given with parameters first.

```ocaml
# [1] ;;
- : int list = [1]```
# [a] ;;
- : 'a list = [1]```
```

Case matching
-------------

```ocaml
 match c with
 | Constructor1 a -> foo a
 | Constructor2 b -> b
```

Named pattern matches
---------------------

Use `[pattern] as [name]` the same way you would use `[name]@([pattern])` in Haskell.

```ocaml
match foo with
  | Bar (Ident s, k) as x::xs ->
```

Modules
=======

Qualified imports
-----------------

A qualified import is done by assigning a module name:

```ocaml
module F = MyFooModule
```

If `MyFooModule` contains a type `type bar = Apa | Bepa`, then the constructors are now accessible with `F.Apa` and `F.Bepa`.

However, we don't need the qualifier if we can infer the correct module from the type:

```
let foo (x : int) : F.bar = if x > 0 then Apa else Bepa
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
