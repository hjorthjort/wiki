Syntaxes
========

There are different syntaxes. The one with `%` for all register names is "GNU x86". The one without, usually, is "NASM".

One thing to keep in mind is that binary instructions in GNU syntax *flip their arguments*, so that `mov %eax, %ebx` moves the contents of `eax` into `ebx`.

Registers
=========

- `eNAME`: 32-bit, e.g., `eax`
- `rNAME`: 64-bit, e.g., `rax`

The `eax` register is the lower 32 bits of `rax`, and same for other registers. 32-bit x86 obviously only uses 32-bit registers.

Calling convention
==================

cdecl
-----

The following convention (not the only one that exists, of course) is called ["cdecl"](https://en.wikipedia.org/wiki/X86_calling_conventions#cdecl).

The calling convention differs slightly between 64-bit and 32-bit. In the 64-bit convention, the first 

The caller

1. For every paremeter i in n..0, `push <parameter_n>`: Pushes the parameters (in reverse order)
2. `call f`: pushes return address, updates instruction pointer to pass control
3. Gets the result of the call in register `[e/r]ax`
4. `add esp <number of parameter bytes>`: deallocate parameters

The callee

1. `push dword ebp`: save value of caller's base pointer
2. `mov ebp, esp`: put this function's base pointer to be right after the stored baspointer of the caller
3. `sub <number of local variable bytes>`: allocate local memory. I guess this can also be done (allocating and deallocating) as variables appear, does not have to be in the prolog
4. Performs the body of the code.
5. `mov esp, ebp`: deallocate locals
6. `pop ebp`: restore caller's base pointer
7. `ret`

The callee may modify the registers: `[e/r]ax`, `[e/r]cx` and `[e/r]dx`. The caller can't rely on them being unchanged after function call (and `[e/r]ax` is actually where the return value is)

The callee must restore (or keep unchanged) `[e/r]bx`, `[e/r]si`, `[e/r]di`, `[e/r]bp`, `[e/r]sp`.
