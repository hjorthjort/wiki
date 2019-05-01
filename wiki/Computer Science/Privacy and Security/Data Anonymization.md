"Anonymizing data" is not as easy as removing identifying attributes, like name,
social security number, or database keys that point to another table with such
information.

There are at least 4 measures of privacy, each with stronger guarantees than the next:

- k-anonymity
- l-diversity
- t-closeness
- differential privacy

The "k", "l" and "t" are used numbers to indicate how strong anonymity is
guaranteed by the measure. So "10-anonymity" would mean that any given person is
indistinguishable from 9 others in the set.

K-anonymity
===========

Arguably the easiest scheme: identify which attributes in a data set are
"quasi-identifiers", i.e., reveal something publicly known about the individual
the entry corresponds to. Examples are age, zip-code, etc, which narrows
down the number of individuals it can refer to.
