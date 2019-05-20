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

Automatic variables
===================

The reference: <https://www.gnu.org/software/make/manual/html_node/Automatic-Variables.html>

`$@` is the name of the target of the rule.

Patterns
--------

Make your life simpler by using patterns:

```make
%.o: %.c
  gcc -c $< -o $@
```

The `%` in the rule will match the same string in both instances, so if i `make
bar.c` it will use (or make) `bar.o`.

- `$*` is the matching stem (what `%` is matching).
- `$<` is the alias for the first prerequisite.
- `$^` is the names of all prerequisites, spearated by spaces. Removes duplicates.
- `$+` is the names of all prerequisites exactly in the order they were specified.
- `$?` is the names of all prerequisites that are newer than the target.

### Dynamic dependencies ###

Check this out:

```make
SIZES=100 1000 10000 100000 1000000 5000000 10000000
foo%:
	echo $@

run: $(SIZES:%=foo%)
```

When calling `make run`, the dependency will be read as "for every element `%` of `SIZES`, `foo%` is a dependency." 

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
