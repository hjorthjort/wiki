The following dependencies made things finally work for me (on Arch Linux). These are from the K Arch package.

```
depends=('java-runtime' 'flex' 'gcc' 'gmp' 'mpfr' 'z3' 'clang' 'libyaml' 'jemalloc' 'opam' 'gawk' 'make' 'diffutils' 'patch' 'tar' 'grep' 'llvm' 'lld')
makedepends=('maven' 'jdk-openjdk' 'cmake' 'boost' 'zlib')
```

For KEVM and KWasm, you need to run the following in order to build the LLVM backend.

```
./deps/k/llvm-backend/src/main/native/llvm-backend/install-rust
```
