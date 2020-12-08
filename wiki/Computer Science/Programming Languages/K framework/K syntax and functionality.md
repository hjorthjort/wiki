# K #

`[prefer]` annotation with `[token]` annotation makes the lexer prefer the one
defined over all others (nice for lexing into your own version of `String`).
`[avoid]` is the inverse
`[functional]` is used by the `haskell-backend`. It sta the symbol is a total function.

# Lists #

Make a list with `syntax MyList ::= List{MyElem, "separator"}`. E.g., `syntax Defns ::= List{Defn, ";"}`. Use `""` as separator for whitespace.
Out of this comes the empty list, `.Defns` (or whatever name you used) and the separator `;`, which can be used as a cons operator.
These lists are abstracted on top of cons lists, so prepending is easy, appending tricky (no builtin).
To prepend, just write the element, the separator, then the list. E.g., rule `D:Defn foo => D ; .Defns`.

Possible way to append:
```k
<log> ... (.List => ListItem({ ACCT | BS1 | BS2 })) </log>
```

# Numerics #

## Division and Modulus ##

Euclidean division is obtained using `modInt` and `divInt`.
`X modInt N = Y modInt N` iff `X = Y` in `Z_N`, i.e. the class of integers mod N.
This is by always choosing the least non-negative representative.
If `N` is negative, the result of `modInt` will still always be positive, i.e., `X modInt N ==Int X modInt -N`.
`X modInt 0` is an error.
`X divInt N` gives the unique quotient in the typical `X = N * Q + R`.

`/Int` implements division symmetrically on positive and negative numbers: `-2 /Int 5 ==Int 0`.
`X %Int N` maintains the sign of `X`, and : `3 %Int -5 ==Int 8 ==Int 5 ==Int 3`, and `-2 %Int -5 ==Int -7 %Int -5 ==Int -2`.
Like with `modInt`, `X %Int N ==Int X %Int -N`.

## Shifting ##

Shifting can only be done by non-negative numbers, i.e., `X <<Int N` and `X >>Int N` only allow non-negative `N`.
NOTE: The Haskell and Java backend accept negative shifts (treating them as shifts in the other direction), but the OCAML and LLVM backend do not.
If `X` is negative, the shift is the same as if `X` was positive with only the sign changing, i.e., `X <<Int N ==Int -(-X << Int N)`.

# Types #

You can explicitly cast values to different types with `{}:>`, e.g., `{X}:>String`. This can be useful when extracting values from a `Map`, for example.

# KLabels #

> KLabel's are the actual nodes in the AST which are used, so `3 +Int 4` would be something like `KApply("_+Int_", [KToken("3", "Int"), KToken("4", "Int")])`. The first argument of the `KApply` is the `klabel`.
> When you define a `token` regex, it becomes a `KToken` in the internal representation.

-- Everett H, public K channel

# Kore #

A list of the attributes for the `kore` language expected and recognized by the
haskell-backend is here:
<https://github.com/kframework/kore/blob/master/docs/attributes.md>

# Syntax vs Semantics #

It can be useful or necessary to separate the syntax and the semantics in different modules.

```sh
kompile
```

In KWasm, we keep the syntax in a separate module that imports the main semantics.
That way, the syntax is inherited from the main module, but we can also introduce a few things to the syntax that can't go in the main module (`WasmInt` and `WasmString` conflict with regular K construct, and K can't deal with it currently).
If you do this, make sure you still introduce the sorts you need in the main module.
So, if `WasmInt` is definied in the syntax module, you can put an empty production in the the main module:

```k
    syntax WasmInt
```

The more usual approach is to have the main module import the syntax module.
That way, all the concrete syntax of the language, and some macros for example, can go in the syntax module.
All further syntax constructs defined in the main module then become part of the abstract syntax only, not the concrete syntax.

# Under the hood #

The OCaml backend basically builds a few thousand lines long match expression, where each rule in the semantics is a case in the match expression.
The LLVM backend is similar.
The Haskell and Java backends uses unification to find out if there is an intersection of the current state and a rule, and if so incorporates that intersection in its possible paths.

# Kast vs. Kore #

Kast is what the Java and OCaml backend speaks, and Kore is what the LLVM and Haskell backends speaks.

# Configuration Cells as Data #


```k
    syntax Bool ::= allPositive(AccountsCell) [function]
 // ----------------------------------------------------
    rule allPositive(_) => true [owise]
    rule allPositive(<accounts>
                       <account>
                         ...
                         <someField> X </someField>
                         ...
                       </account>
                     </accounts>
                     )
      => false
    requires X <Int 0
    
    ...
    
    rule <k> #allSomeFieldPositive => allPositive(<accounts> ACCS </accounts>) ... </k>
         <accounts> ACCS </accounts>
```

Reference image from pair programming:

![](passing_cells.png)
