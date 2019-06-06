Control flow
------------

A **branch** (`br` of `br_if`)[^3] takes an operand `i`, and, like de Bruijn indexing for control flow, jumps `i - 1` control structures outwards.
In the case of `block` or `if ... else` structures, it will jump to the end; in the case of `loop` structures, it will resume the loop from the beginning. That means that `loop` blocks don't actually loop when the end is reached; instead the loop is exited by reaching the end of it; to actually loop back, one ends the loop block with a branch instruction.
One can think of the branching instructions as `continue` in many imperative languages when the target is a `loop`, and a `break` when the target is a `block` or `if .. else`.

It is possible to branch to a function body, which is a type of block, which is the same as returning from a function.
Additionaly, it is possible to 


### Labels

A label carries an arity (how many values to return, currently only allows 1 value) and a continuation: what to do when branching to that label.
The idea is that when branching to a label, the continuation contained in it is popped out and executed.
Only `loop`s actually put code in the continuation; `block`s and `if...else`s put in an empty continuation.
`loop`s put themselves -- the entire `loop` construct -- in the continuation, and thus branching to their labels is just re-executing the loop: replacing the previous label with a new one, and repeating.
When execitng a loop without branching, the continuation is thrown away.

Memory
------

### Optimization

A memory page is 64 KiB, (`2^16 = 1024 * 64 bytes`), because that is the *least common mutliple* of minimum page sizes on common hardware. So when growing memory in WebAssmebly, the hardware will often give away more than asked for (several memory pages) and it's the runtime system's job to ensure that too high addresses are not accessed, and that more meory can be allocated "for free" when the operating system has allocated has allocated more memory than was initially asked for.

Memory access can be aggressively optimized.
For one, a JIT compiler can use the (known) memory size, the static offset and type size as a constant values, constant-fold them, and compare to the dynamic portion of the address, i.e., if the check requires is to make sure that `k + o + |t| - 1 < memsize`[^1]  where `k` is dynamic, `o` is the static offset and `t` is the type, this can be rewritten by a JIT compiler to `k < memsize - o - |t| + 1`, where the right hand side is completely constant.
Care must be taken to invalidate and recompute the constant after memory growth.

Most 64 bits systems also have some sort of virtual memory hardware.
With such hardware, certain pages can be marked accessible or inaccessible by a process.
The engine then marks all accessible pages as such, and relies on hardware to catch out-of-bounds access.

### Security

> Linear memory is disjoint from code space, the execution stack, and the engine’s data structures; therefore compiled programs cannot corrupt their execution environment, jump to arbitrary locations, or perform other undefined behavior. At worst, a buggy or exploited WebAssembly program can make a mess of the data in its own memory. This means that even untrusted modules can be safely executed in the same address space as other code.
-- [Bringing the Web up to Speed with WebAssembly](https://people.mpi-sws.org/~rossberg/papers/Haas,%20Rossberg,%20Schuff,%20Titzer,%20Gohman,%20Wagner,%20Zakai,%20Bastien,%20Holman%20-%20Bringing%20the%20Web%20up%20to%20Speed%20with%20WebAssembly.pdf)

[^1]: This is the actual requirement for getting a value of type `t` with the isntruction `(i32.const k) (t.load offset=o align=a)`.

[^3]: Note: `br` should be read "branch" rather than "break"; although "break" makes sense for blocks and ite-statements, it doesn't make sense for `loop`, because in `loop` a branch means "repeat from the top of this loop": this is how loops get repeated.

Tips and tricks
===============

Standard `.wast` function has the header
```
(func $internalName (export "funcName") (param i64) (result i32) (local i64)
```

Use `$internalName` to `call`, and `"funcName"` to `invoke` or `import`.


While loop with index (for loop)
--------------------------------

- Declare a local for your loop index (i32 or i64). It is initialized to 0.
- Make a block with a loop in it
- To break out of the loop, branch to the block label.
- To continue the loop, branch to the loop label.

Example:

```
func (export "foo") (local i32)
  block
    loop
      ;; Do something. If you need to break, call br to the block.
      (local.tee (i32.add (local.get 0) (i32.const 1))) ;; Increment
      i32.const 10 ;; Num iterations.
      i32.lt
      br_if 0 ;; Redo the loop.
    end
  end
```
