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

The goal has no variables, so an answer is just true or false. Scanning the knowledge base from top to bottom, parent(tom, liz) fails because bob can't unify with liz (since they are different atoms), and parent(tom, bob) matches, so first is true. On backtracking, the remaining parent facts start with the argument bob, pat, or ann, which can't unify with tom, so there is no second and the count is 1.

## P03

The goal scans the parent facts from top to bottom, and a fact matches only if the atom tom unifies with its second argument, since the variable X can unify with any atom in the first argument. The second arguments of the parent facts are liz, bob, ann, pat, jim, and sue, and none of them can unify with tom, so there are no matching facts. So the first is false because the query has no answer at all, second is none because there is no other answer to finc, which makes the count 0.

## P04

The goals are tried from left to right, and = is unification, so nothing is computed. X = f(Y, a) binds X to the term f(Y, a) with Y still unbound inside it. Y = b then binds Y to b, and since Y is part of the term X holds, X becomes f(b, a), so first is X=f(b,a),Y=b. Neither goal has another clause to try, so there is no second answer and the count is 1.

## P05

It is just a unification between two compound terms and both terms have the functor f and arity 2, so the arguments are unified pairwise: X binds to a, and b unifies with Y by binding Y to b. The bindings are consistent, so first is X=a,Y=b. Unification offers no other way to match, so there is no second answer and the count is 1.

## P06

It is just basically a unification between two atoms a and b. Two different atoms can't unify, and there is no variable that could be bound to make them the same. The goal fails right away, so first is false because the query has no answer at all, second is none, and the count is 0.

## P07

The body of the query grandparent(tom, X) becomes parent(tom, P) and parent(P, X), tried from left to right. P binds to liz first, but parent(liz, X) fails since no parent fact starts with liz, so the search backtracks and rebinds P to bob, where parent(bob, X) matches ann first (X=ann) and then pat (X=pat) on backtracking. No bob or tom facts are left after that, so the count is 2 before false.

## P08

The base clause ancestor(A, D) :- parent(A, D) is tried first, so parent(tom, X) makes X binds to liz and then bob (X=liz and X=bob), which are first and second. The recursive clause then runs parent(tom, P) and ancestor(P, X), where P=liz fails because liz has no children, and P=bob gives ann and pat from its base clause, then sue through ann and jim through pat. The full order is liz, bob, ann, pat, sue, jim, so the count is 6.

## P09

The recursive clause of ancestor_r comes first, so the query runs parent(tom, P) and ancestor_r(P, X) before it reads any direct parent fact. P=liz fails because liz has no children, and P=bob goes down to ann and pat, whose own base clauses give sue and jim first, which are first and second. The base clause of bob then gives ann and pat, and the base clause of tom gives liz and bob, so the order is sue, jim, ann, pat, liz, bob and the count is 6.

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

