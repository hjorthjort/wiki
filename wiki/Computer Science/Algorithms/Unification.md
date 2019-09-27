Main source: https://www.researchgate.net/profile/Ugo_Montanari/publication/200034204_An_Efficient_Unification_Algorithm/links/55f1a87608ae199d47c3faea.pdf

You are given 2 terms in a language, `t` and `t'`.
They may contain functions, constants, and variables.
For example, `f(x_1, g(x_2), c)`, where `x_1` and `x_2` are variables, `g` is a unary function, and `c` is a constant.

The two terms are then equated: `t = t'`.
The problem: *Find a substitution of the variables that makes this equation hold.*
This substitution is called a **unifier**.
Examples:
- `f(x) = f(y)` for `x` and `y` has any substitution `s = x => a, y => a` as a unifier, for any `a`.
- `f(x) = x` has no finite unifier, only the recursive substitution `s = x => f(x)`, which when applied yields a new unification problem.
- `g(x) = f(x)` has no unifier.

Substitution
============

A substitution is a map from variables to terms.
Applying the substitution generally just means applying it once.
So the substitution `x => y, y => a` applied to `x + y` becomes `y + a` after one application, and `a + a` after appying the same substitution to the result.
Call `A_s(t)` the application of a substitution `s` to a term `t`.
When trying to find a unifier, we are looking for a substitution which makes the sides of the equation equal in a single step. 

More than one equation
======================

We often deal with more than a single equation, and instead have several: `t_1=t_1', ... , t_n=t_n'`.
A **unifier for a set** of equations is simply a _single_ substitution which makes all of the equations hold.

Term reduction
==============

Recall that an equation like `f(x_1, ..., x_N) = g(y_1, ..., y_M)` has no unifier, even when `N=M`.
So the "root" symbol of the term (i.e., the top-level constructor, the root node of the parse tree if it is not a variable, etc.) must agree.
So the interesting case is `f(x_1, ... , x_N) = f(x_1', ... , x_N')` remains.
This single equation can be replaced by the set of equations `x_1 = x_1', ..., x_N = x_N'`.
I.e., a unifier of the original equation is any substitution which unifies the arguments pairwise, and the root symbol `f` is dicarded.
We say that this set of equations is equivalent to the original equation.

Variable elimination
====================

If a set of equations contains the equation `x = t` where `x` is a variable and `t` is any term (which may contain variables), we may apply the substitution `x => t` to the all equations in the set _except_ to `x = t`, and the set of equations thus obtained is equivalent.

Note, however, that if `t` contains `x` unification is not possible, because the unifier would have to contain infinite terms (allowing this would be an extension of what we mean by a unifier).
If `t` does not contain `x`, you can keep doing this substitution until you reach a fixed point, i.e., where `x` is no longer present in any of the equations, and you may then apply `x => t` to `x = t`.
You will then obtain the trivial equation `t = t`.

Solution and Most General Unifier (mgu)
=======================================

Solved form
-----------

Obtaining a unifier can be reduced to the problem of massaging the set of equations into a form from which the substitution is obvious.
This is called the *solved form* of the set of equations.
It has the following properties:

1. All the equations have the form `x_i = t_i, i=1,...,k`, where `x_i` is variable and `t_i` is a term.
2. All the variables on the left (`x_i`) appear only there, and not in any of the right-hand terms.
   This is mostly for efficiency: such a unifier unifies the terms with a single substitution.
   It also guarantees that the unifier does not require infinite terms.

Algorithm
---------

A simple,  non-deterministic algorithm for finding the mgu is the following, which will make the set of equations converge into solved form.

At each step, do one of the following steps.
When neither step applies, the set of equations is in solved form and the mgu is obtained.[^1]
The algorithm may stop with failure before, if unification is not possible.
Below, `x` is always a variable and `t` is always a term.

* Find an equation of the form `f(x_1) = g(...)`. If `f==g` (either syntactically or under some theory that is being used), then apply term reduction.
* Find an equation of the form `x = t`, where `x=/=t`, and apply variable elimination.
* Find an equation of the form `x = x` and eliminate it[^2].
* Find an equation of the form `t = x` and turn it into `x = t`, where `t` is a non-variable term and `x` is a variable.

[^1]: Proof that it finds the `mgu`?

[^2]: Why not apply this rule to all `t = t` where `t` is any term? For efficiency?

