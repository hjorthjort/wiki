Based on lectures of proof theory by Frank Pfenning.

Terminology
===========

Propositions, predicates and judgements
---------------------------------------

* proposition: statement that has a truth value. Example: "Aristotle is American", "Gödel proved first-order-logic sound and complete".
* predicate: roughly "a proposition with variables". I like to think of a predicate as a set of tuples, where the predicate is true for all tuples in the set.
Example 1:  If `P(x,y)` is how I write my predicate predicate and the intended meaning is "`x` is the father of `y`", then `P` is a set and `(Stellan Skarsgård, Alexander Skarsgård)` is a member of `P`[^1]
Example 2: `N(x)`, means "`x` is a natural number", then `N` is the set of natural numbers.
* judgement: A statment about the truth value (or otherwise meaningful value, such a "will eventually be true") for a predicate or proposition.

Logic design
============

For every *connective*, e.g., conjunction, implication, negation, etc., the *introduction rules* and *elimination rules* are in said to be "in harmony" iff they are
* locally sound
* locally complete

Local soundness
---------------
Intution: the elimination rules can't be too strong: they can not give us any information that isn't already "packed into" the connective.

Formally: *For all* elimination rules of a connective, the resulting judgement can also be found in the proof that introduced the connective.

### Example:

Conjunction has two elimination rules.
If we use the first elimination, on line a, we find that in order for the conjunctive to have been introduced (line 3), there must have been preceeding already a proof that "p is true", so the elimination rule doesn't give any new judgement that is not already present in the proof that lead to the introduction.
Indeed, we find that A is already a proof of "p is true".
Likewise for line b.


| Judgement        | Rule         | Line |
|------------------|--------------|------|
| p  is true       | Some proof A |    1 |
| q  is true       | Some proof B |    2 |
| p /\ q   is true | /\I  1, 2    |    3 |
| ...              | ...          |    4 |
| p  is true       | /\E__1 3     |    5 |
| q  is true       | /\E__2 3     |    6 |
|                  |              |      |



Local completeness
------------------
Intution: the elimination rules should not be too weak, and using them we should be able to extract *all* the information we "packed" into the connective.

Formally: If a connective has elimination rules, *there exists* a way to eliminate the connective, often several times (from the same introduction proof) so that the from the resulting judgments the connective can be reintroduced.

### Local expansion

Is what we call the witness of local completeness.

[^1]: To the best of my knowledge.
