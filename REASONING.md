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

Following source order, parent(tom, X) finds two facts. The first one is parent (tom, liz), where X binds to liz. The first ; finds the next matching fact, parent(tom, bob), so then X binds to bob. Nothing follows, the search ends after 2 answers.

## P02

parent(tom, bob) matches the parent/2 fact in the base, so it returns true. 

## P03

Parent facts are scanned in source order, none of them have X first. The query returns false, and since no other fact unifies, there is no second answer. Search ends at 0 answers.

## P04

X is first unified with f(Y,a), and Y is unbound. The second part binds Y to b, so X is now f(b,a). Unification, then nothing else, so the query returns X = f(b,a).

## Correction for X = f(Y, a), Y = b

I didn't include Y=b, the prompt printed it with X in the first answer. I interpreted the binding and unification in one step, melding Y and b together.

## P05

The two sides'arguments can be paired under unification so X = a and Y = b.

## Correction for X = f(Y, a), Y = b
I mistakenly put Y=b in the second answer and the prompt printed them both in the first.

## P06

a and b are different atoms, and different atoms can't be unified by any rule, so the query gives false.

## P07

Grandparent rule says G is a grandparent of C if it's a parent of P, and P is a parent of C. The facts are scanned following the source order, finding 2 matches with tom first, liz and bob. X binds to liz first, then we scan for parent(liz, X). Since liz has no child, it backtracks to bob and does the same. The query comes up with two facts: parent(bob, ann), and parent(bob, pat). X binds to ann, then to pat after the first ;. Search ends after 2 answers.

## P08

The base ancestor clause gives that tom is a parent and therefore ancestor of liz and bob. Backtrack, then bob has ann and pat as children. Other clause gives sue, child of ann and jim, child of pat. Folowing the order starting with the base, the answers are liz, bob, ann, pat, sue, jim.

## P09

Same initial steps as P08, skip to the first clause on bob: parent(bob, P2), and P2 binds to ann first. ancestor_r(ann, D) is called, then applies the clause again with P3 binding to sue, whose ancestor_r call has no matches. The second clause: parent (ann, X) is then applied; X binding to sue, backtracking to bob's second child, pat, where the same bind logic is done for her child jim. No more children follow, so the second clause parent(bob, X) is applied, binding ann and pat before going back up to tom, which ends the search with X=liz and X=bob, still at 6 answers but kind of reversed.

## P10

The first match for X is parent(pat, jim). X binds to pat, and the first clause of the rule applies, pat is an ancestor of jim. Under clause 2, we have ancestor(P, jim), and we find P = pat. X is bob, then P = bob gives X = tom. Nothing else passes, so we stop with pat tom and bob.

## P11

Recursive rule says that d can be reached from a if Z can be reached from a, and d can be reached from Z, and if there is an edge from a to d. On the first clause: reach (a, Z1), edge (Z1, d), and reach (a, Z1) gives reach (a, Z2), edge (Z2, Z1). The pattern of endless Zs continue since there's no bound to return the recursion to its base clause, it won't give an answer and will continue being stuck in clause 1.

## Correction for reach(a, d)
I predicted error for count instead of unbounded which is what was printed. Didn't check that count could hold an unbounded value

## P12

First clause checks edge(a, d), which matches nothing. Second clause follows edge(a, b), then recurses on reach_r(b,d), following edge (b, c), and recurses on reach_r(c,d). Finally, first clause finds edge(c,d), so it answers true. 

## Correction for reach_r(a, d):
Predicted none for second, 1 for count when it should be true and unbounded. Assumed directly from the first answer, but in deeper traces it cycles back to unbounded.

## P13

Base clause doesn't apply because there is no [], so query moves to the recursive one. It finds len[b,c], [c], and []. N is M + 1 steps and for each level 1 is added. From the base N 0, 3 are added, so N=3.

## P14

The first clause makes X = [], Y = [1,2], L = [1,2] also. The second clause takes the first one, and the call recurses to join (T, L, [2]). Splitting again, it gives X=[1] and Y=[2]. Recursing, there's join(T2, L2, []), and after splitting: X = [1,2], Y=[]; which can't be unified with [H|R], so it ends at 3 answers.

## P15

X isn't bound somewhere before X > 2 was called. The rules need binding before anything, so there's an error.

## Correction for X > 2, elem(X, [1, 2, 3])
Put none in the answers when it should have been error. Didn't double check terms

## P16

X is unbound, the first clause recurses to each of the elements in the list. Going through X=1 and X=2, only X=3 satisfies the condition and is returned.
