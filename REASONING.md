<!--no-pdf-->
# Why Each Prediction Reads the Way It Does

Write one entry per case, before you run anything. Name the rule behind each
predicted field. One to three sentences is enough. The grader checks the
structure of this file only. It does not judge how good the reasoning is, and
it sets no word minimum.

For every case you predicted wrongly, add a short correction after you run
it: what you predicted, what the language printed, and which rule you
misread. Corrections belong in the correction commits, not the prediction
commit.

Commit this file together with `predictions.tsv`, before any case runs:

```bash
git add predictions.tsv REASONING.md
```

The example below uses a query that is not one of the 16 cases, so it gives
no answer away. `parent(pat, X)` reads `parent(pat, jim)` because the `parent`
facts are tried top to bottom and only that one has `pat` as its first
argument:

```text
## parent(pat, X)

The goal scans the parent facts in source order. Only parent(pat, jim) has
pat first, so X binds to jim. No second fact matches, so the first ; finds
nothing and the search ends after one answer.
```

## P01

The goal scans the parent facts from top to bottom, and a fact matches only if the atom tom unifies with its first argument. parent(tom, liz) matches first, binding X to liz (hence, X=liz for first), and on backtracking parent(tom, bob) matches next, binding X to bob (hence, X=bob for second). The other parent facts start with the argument bob, pat, or ann, which can't unify with tom, so no matching fact is left after parent(tom, bob) and the count is 2 before false.

## P02

The goal has no variables, so an answer is just true or false. Scanning from top to bottom, parent(tom, liz) fails because bob can't unify with liz (since they are different atoms), and parent(tom, bob) matches, so first is true. On backtracking, the remaining parent facts start with the argument bob, pat, or ann, which can't unify with tom, so there is no second and the count is 1.

## P03

Write one to three sentences here. Name the rule that fixes each field.

## P04

Write one to three sentences here. Name the rule that fixes each field.

## P05

Write one to three sentences here. Name the rule that fixes each field.

## P06

Write one to three sentences here. Name the rule that fixes each field.

## P07

Write one to three sentences here. Name the rule that fixes each field.

## P08

Write one to three sentences here. Name the rule that fixes each field.

## P09

Write one to three sentences here. Name the rule that fixes each field.

## P10

Write one to three sentences here. Name the rule that fixes each field.

## P11

Write one to three sentences here. Name the rule that fixes each field.

## P12

Write one to three sentences here. Name the rule that fixes each field.

## P13

Write one to three sentences here. Name the rule that fixes each field.

## P14

Write one to three sentences here. Name the rule that fixes each field.

## P15

Write one to three sentences here. Name the rule that fixes each field.

## P16

Write one to three sentences here. Name the rule that fixes each field.

