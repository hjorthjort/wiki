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

## `--boundary-cells`

TLDR: Invoke `kprove` with the flag `--boundary-cells k`.

If you write a spec like this

```k
rule <k> FOO => BAR ... </k>
```

the three dots at the end can come back to bite you.
Remember that they represent any continuation in the `<k>` cell.

```k
rule <k> (FOO => BAR) ~> CONT </k>
```

It is entirerly possible, for some semantic, that this could happen:

```k
rule <k> CONT => BAR ~> CONT2 </k>
```

So if K gets to a point where it can't prove that `FOO` goes to `BAR` without touching `CONT`, it will try `CONT`, which can be any program.
This leads to explosion, and in the worst case will trigger edge case bugs in your semantics that you didn't consider.

## Pattern matching vs `requires`

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

## Debugging Haskell Backend

You can pass the flag `--haskell-backend-command kore-repl` to `kprove`.

From Virgil:

> Instead of running the haskell backend with `kore-exec`,  you can run it with `kore-repl` with the same command line flags, and then you can step through your proof. If you're lucky, `step 100` or some other large number will run the repl until the error you mentioned before, but the repl stops at branching points and you need to select a branch (`select <number>`) and restart it manually (`step 100 again`). You also have nice things like displaying past and current configurations (`config <number>`), the execution graph (`graph`) and so on (help to see all options).

Even better, do

```
cd .build/defn/haskell  # Or wherever your semantics were built with kompile.

LEMMAS_FILE=KEWASM-LEMMAS
SPEC_FILE=../../../tests/proofs/invoke-contract-spec.k
HASKELL_BACKEND="$HOME/ewasm-semantics/deps/wasm-semantics/deps/k/haskell-backend/src/main/native/haskell-backend" \
BIN_DIR="$(cd $HASKELL_BACKEND; stack path --local-install-root)/bin" \
sh -c \
  'kprove --debug --dry-run \
      --haskell-backend-command "$BIN_DIR/kore-repl \
      --repl-script $HASKELL_BACKEND/dist/kast.kscript" \
      -d . \
      -m $LEMMAS_FILE \
      $SPEC_FILE'
```

This will print the command that was run, which you can invoke.
That is a lot faster than going through `kprove` every time, since that actually rebuilds the semantics (in a `.kprove-...` directory)

