# Proof system

## Circularity

What allows matching logic to prove loop invariants, make coninductive proofs, etc.

When proving a reachability claim, `kprove` treats every reachability claim in a module as true, and can use them as lemmas. However, `kprove` will only give a final `#True` result if it can prove *every* claim in the module true.

* Question: isn't that dangerous? If I make false claim in one reachability claim, and the same false claim in another claim, wouldn't I be able to prove them using eachother, in the end proving something false? *

# Annotations
`[concrete]`
------------

Means: don't apply this rule unless the values are fully available, i.e., don't try to reason about whether the rule shold apply, just apply it when you can actually evaulate it.

`[trusted]`
-------------

`[trusted]` on a rule (after `requires` clauses) makes `kprove` accept it as true without proving it. That means it can also discharge it and use in other proofs.

# Things to keep in mind

It may make sense to avoid pattern matching in rules when possible.
The following rules may look equivalent, but they are not.

```k
rule Foo ( X:Int, X:Int ) => X
rule Foo ( X:Int, Y:Int ) => Y requires X ==Int Y
```

The drawback is that **pattern matching uses `==K` under the hood!**
So the above rule would essentially be

```k
rule Foo ( X:Int, Y:Int ) => X requires X ==K   Y
rule Foo ( X:Int, Y:Int ) => Y requires X ==Int Y
```

The problem with this is that a prover may use some specialized reasoning for `==Int` that is not available for `==K`.
So the better approach is the bottom one.
