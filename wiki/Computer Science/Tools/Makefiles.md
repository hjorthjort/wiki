In their simplest form, Makefiles are used to create files by applying rules.

Need the file `foo`?

```sh
make foo.o
```

In the Makefile, there would be a rule:

```make
foo.o: foo.c
  gcc -c foo.c -o foo.o
  
foo.c:
  echo "int main() { return 0; }" > foo.c
```

This rule must create a file called `foo.o`. That's the point of it.

If the file `foo.c` does not exist or is out-of-date (because one of its
dependencies is), it will be created using the rule for it.

**Always indent using only tabs, no spaces.**

**You can break lines by ending them in "\"**

Patterns
========

Make your life simpler by using patterns:

```make
%.o: %.c
  gcc -c $< -o $@
```

The `%` in the rule will match the same string in both instances, so if i `make
bar.c` it will use (or make) `bar.o`.

- `$<` is the alias for the first prerequisite.
- `$^` is the names of all prerequisites, spearated by spaces. Removes duplicates.
- `$+` is the names of all prerequisites exactly in the order they were specified.
- `$?` is the names of all prerequisites that are newer than the target.

`.PHONY`
========

In practice, some rules will not be created to make files that are named the same as the rules. `make clean` and `make build` are common examples. These should be listed as `.PHONY`.

```make
.PHONY: build clean
```

Listing as `.PHONY` means `make` will not look for a file of that name, but just do that rule.


Flags
=====

- `-jN` means use `N` threads. E.g., `-j3` means run with 3 threads.
- `-d` prints debug output.
  Good for learning how the `make` program actually resolves rules.
  Also good to see why a rule is failing: it may not be the one that `make` says it fails on that is the problem.
