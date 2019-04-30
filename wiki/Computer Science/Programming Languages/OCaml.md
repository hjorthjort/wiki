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
`#use` essentially takes the file and copies it all into the REPL (silently).

```ocaml
#use "Foo.ml" ;;
open Foo ;;
Foo.myval ;;
- : int = 10
```

Display the interface of a module:

```ocaml
#show Foo ;;
module Foo : sig val myval : int end
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

To find a package and make it possible to open:

```ocaml
#require "core" ;;
open Core.Monad ;;
```

Packages get dot-separated names:

```ocaml
#require "jlc.typechecker" ;;
open Jlc_typechecker.SomeModule ;;
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
- : int list = [1]
# [] ;;
- : 'a list = []
# Some "hello" ;;
- : string option = Some "hello"
# None ;;
- : 'a option = None
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

Mutable references
------------------

```ocaml
# let a = ref 42 in
let b = a in
(b := -42 ; !a) + !a ;;
- : int = 0
```

Modules
=======

<https://ocaml.org/learn/tutorials/modules.html>

Imports
-------

`open [MODULE NAME]`: Regular import into your namespace, but doesn't re-export that module in your module.
`include [MODULE NAME]`: Regular import to your namesapce, and re-exports all of that module.

These make the entire top level of that module available, as if you were writing code in that module.


### Qualified imports ###

A qualified import is done by assigning a module name:

```ocaml
module F = MyFooModule
```

If `MyFooModule` contains a type `type bar = Apa | Bepa`, then the constructors are now accessible with `F.Apa` and `F.Bepa`.

However, we don't need the qualifier if we can infer the correct module from the type:

```
let foo (x : int) : F.bar = if x > 0 then Apa else Bepa
```

Module interfaces, a.k.a. signatures
------------------------------------

Modules are implicitly typed.
They can be implictly typed, which limits what values they export (the implementation must be a proper subtype of the declared type).
The type can be exported and used to type other modules.

```ocaml
module type Hello_type : sig
  val fooo : string -> bool
end

module Hello : Hello_type = struct
  val fooo s = String.length s >= 2
end
```

Functors
--------

A module parameterized by anohter module.

Jane Street
===========

A very cool company (apparently) that work only in OCaml with High Frequency
Trading. Apparently every functional programmers dream. They are founding a lot
of FP research.

`core` library
--------------

They built their own standard library for OCaml. It's great.

The docs: <https://ocaml.janestreet.com/ocaml-core/latest/doc/core>
