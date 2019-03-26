# Normal forms #

Conjunctive normal form: try to satisfy *all* these clauses, where there may be many ways to satisfy a single clause.

```
(A \/ B) /\ (C \/ B) /\ ~B
```

Disjunctive normal form: try to satisfy *any* of these clauses, where each clause is strict on how it can be satisfied.


# The DPLL Algorithm  #

Name after Davis–Putnam–Logemann–Lovelan.

Based on <http://homepage.divms.uiowa.edu/~ajreynol/pres-dpllt15.pdf>

Works on formulas in *conjuncitve normal form*.

Alternates between

- propagating what it can infer (assigning truth values it can be sure of) and
- deciding
  - just pick a truth value for a variable
  - recursively try to solve the new formula

Keep doing this until a result can be returned (from the current decision branch):

If a state is reached where

* *all* clauses are `true`, return `SAT`.
* *any* clause is `false`, return `UNSAT`.

If any decision branch yields `SAT`, return `SAT`. If no decision branch yields `SAT`, return `UNSAT`.
  
## Example: ##

```
(A \/ B \/ C) /\ (B \/ D) /\ (~B \/ ~A)
```

Decide `A=true`, label it `BRANCH 1`

```
( true      )) /\ (B \/ D) /\ ( ~B    )
```

Propagate `B=false`

```
( true      )) /\ (     D) /\ ( true  )
```

Progate `D=true`

```
( true      )) /\ ( true ) /\ ( true  )
```

return `SAT` from `BRANCH 1`

return `SAT`

# SMT and DPLL(T) #

SMT is satisifiability with some theory, for example, linear integer arithmetic (LIA).

Formally, a theory T is a pair ( S_T, I_T ), where: 
- S_T is set of function symbols, the "signature" of T
  - E.g. S_{LIA} = { +, -, <, <=, >, >=, 0, 1, 2, 3, ... }
- I_T is a set of interpretations for T 
  - E.g. each interpretation in I_{LIA} interprets functions in S_{LIA} in standard way:
     - 1+1 = 2, 1+2 = 3, ...
     - 1 > 0 = true, 1 > 1 = false
     - ...
- A formula F is T-satisfiable if there is an interpretation in I_T that satisfies F

## DPLL(T) ##

Intuition: treat all statements of the theory as opaque, propositional variables. Use SAT to find a satisfiable assignment to the variables.
Only when necessary, invoke the special theory solver.
If the solver finds some statements can't be simultaneously true, add that to the original formula as propositions, and keep going.

### Example with DPLL(LIA) ###

LIA is, again, linear integer arithmetic.

```
( x+1>0 \/ x+y>0 ) /\ ( x<0 \/ x+y>4) /\ ~x+y>0
```

Turn into SAT.

```
( A     \/     B ) /\ ( C   \/    D ) /\ ~B
```

Conclude `B=false` and propagate.

```
( A              ) /\ ( C   \/    D ) /\ true
```

Conclude `A=true` and propagate.

```
( true           ) /\ ( C   \/    D ) /\ true
```

Decide `C=true` (BRANCH 1)

```
( true           ) /\ ( true        ) /\ true
```

The assigment is now:

```
A=true    x+1 >  0
B=false   x+y <= 0
C=true    x   <  0
```

A theory solver can figure out that this can't be satisfied: `x < 0 => x + 1 <= 0`

So we add `(~A \/ ~C)` to our set of clauses, because of the offending case
above. backtracking again, we realize we must now propagate `C=false`.

```
( true           ) /\ (           D ) /\ true
```

Conclude `D=true` and propagate

```
( true           ) /\ (        true ) /\ true
```

The assigment is now:

```
A=true    x+1 >  0
B=false   x+y <= 0
C=false   x   >= 0
D=true    x+y >  4
```

This also fails because `B` can't be false and `D` true at the same time.

Add `~(~B /\ D) == (B \/ ~D)` to our clauses.
With the two additons, we get

```
( A     \/     B ) /\ ( C   \/    D ) /\ ~B /\
( ~A    \/    ~C ) /\ ( B   \/   ~D )
```

wich is unsatisfiable.

