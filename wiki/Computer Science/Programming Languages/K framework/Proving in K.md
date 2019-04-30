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


`[concrete]`
------------

Means: don't apply this rule unless the values are fully available, i.e., don't try to reason about whether the rule shold apply, just apply it when you can actually evaulate it.
