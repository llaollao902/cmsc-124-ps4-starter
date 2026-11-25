# The Search and the Stack

Replace this paragraph after your checks pass. Paste the output of `./trace`
for one case. Then answer three questions about it.

== trace P08 ==
Call: ancestor(tom,X)
  Call: parent(tom,X)
  Exit: parent(tom,liz)
Exit: ancestor(tom,liz)
answer 1: X=liz
Redo: ancestor(tom,liz)
  Redo: parent(tom,liz)
  Exit: parent(tom,bob)
Exit: ancestor(tom,bob)
answer 2: X=bob
Redo: ancestor(tom,bob)
  Redo: parent(tom,bob)
  Fail: parent(tom,X)
  Call: parent(tom,_)
  Exit: parent(tom,liz)
  Call: ancestor(liz,X)
    Call: parent(liz,X)
    Fail: parent(liz,X)
    Call: parent(liz,_)
    Fail: parent(liz,_)
  Fail: ancestor(liz,X)
  Redo: parent(tom,liz)
  Exit: parent(tom,bob)
  Call: ancestor(bob,X)
    Call: parent(bob,X)
    Exit: parent(bob,ann)
  Exit: ancestor(bob,ann)
Exit: ancestor(tom,ann)
answer 3: X=ann
Redo: ancestor(tom,ann)
  Redo: ancestor(bob,ann)
    Redo: parent(bob,ann)
    Exit: parent(bob,pat)
  Exit: ancestor(bob,pat)
Exit: ancestor(tom,pat)
answer 4: X=pat
Redo: ancestor(tom,pat)
  Redo: ancestor(bob,pat)
    Redo: parent(bob,pat)
    Fail: parent(bob,X)
    Call: parent(bob,_)
    Exit: parent(bob,ann)
    Call: ancestor(ann,X)
      Call: parent(ann,X)
      Exit: parent(ann,sue)
    Exit: ancestor(ann,sue)
  Exit: ancestor(bob,sue)
Exit: ancestor(tom,sue)
answer 5: X=sue
Redo: ancestor(tom,sue)
  Redo: ancestor(bob,sue)
    Redo: ancestor(ann,sue)
      Redo: parent(ann,sue)
      Fail: parent(ann,X)
      Call: parent(ann,_)
      Exit: parent(ann,sue)
      Call: ancestor(sue,X)
        Call: parent(sue,X)
        Fail: parent(sue,X)
        Call: parent(sue,_)
        Fail: parent(sue,_)
      Fail: ancestor(sue,X)
      Redo: parent(ann,sue)
      Fail: parent(ann,_)
    Fail: ancestor(ann,X)
    Redo: parent(bob,ann)
    Exit: parent(bob,pat)
    Call: ancestor(pat,X)
      Call: parent(pat,X)
      Exit: parent(pat,jim)
    Exit: ancestor(pat,jim)
  Exit: ancestor(bob,jim)
Exit: ancestor(tom,jim)
answer 6: X=jim
Redo: ancestor(tom,jim)
  Redo: ancestor(bob,jim)
    Redo: ancestor(pat,jim)
      Redo: parent(pat,jim)
      Fail: parent(pat,X)
      Call: parent(pat,_)
      Exit: parent(pat,jim)
      Call: ancestor(jim,X)
        Call: parent(jim,X)
        Fail: parent(jim,X)
        Call: parent(jim,_)
        Fail: parent(jim,_)
      Fail: ancestor(jim,X)
      Redo: parent(pat,jim)
      Fail: parent(pat,_)
    Fail: ancestor(pat,X)
    Redo: parent(bob,pat)
    Fail: parent(bob,_)
  Fail: ancestor(bob,X)
  Redo: parent(tom,bob)
  Fail: parent(tom,_)
Fail: ancestor(tom,X)
== 6 answers ==

In the P08 trace, the lines that push a frame onto the runtime stack are the Call lines. Each one starts a new goal one level deeper, so the indentation shows the stack depth. For example, just before answer 5, Call: ancestor(ann,X) is at depth 2 (start at 0) and Call: parent(ann,X) is at depth 3, nested inside the resumed Redo: ancestor(bob,pat) and Redo: ancestor(tom,pat). That means three goals are suspended while the deepest one runs.

The lines that pop a frame are the Exit and Fail lines. Right before answer 5, Exit: ancestor(ann,sue), Exit: ancestor(bob,sue), and Exit: ancestor(tom,sue) pass sue back one level at a time, like return addresses being followed. Earlier, before answer 3, Fail: parent(liz,X) pops a frame and returns nothing. Only a Fail leaves nothing behind. An Exit hands back a value, but as the Redo lines show, the goal can still be resumed.

That is what a Redo line uses. A goal resumed by a Redo still knows which clause it matched, which clauses are left to try, and which bindings it must undo. After answer 1: X=liz, Redo: ancestor(tom,liz) leads to Redo: parent(tom,liz) and then Exit: parent(tom,bob). This works because parent(tom,X) had already exited but still remembered that it matched liz, that bob was untried, and that X=liz needed undoing. A returned frame had already thrown all of this away, since its locals and return address are gone once it pops. The same thing appears after answer 5, where the Redo lines on ancestor(tom,sue), ancestor(bob,sue), and ancestor(ann,sue) show each exited goal still holding its own record of what was left to try.

One of reach/2’s clauses is left-recursive. The recursive clause's first goal is reach (X, Z), which Prolog selects repeatedly, so it never returns. No read edges, so no printed answers. When the base clause goes first, edge(a, d) fails, but backtracking into the recursive clause with the base case already tried, Prolog can match the clause against edge(a,b). Recursive clause goals give edge(b,c) and edge(c,d), completing descent from a to d.

For ancestor/2, the recursion depth is the same regardless of what clause comes first. The recursive clause is guarded. The first goal is parent(A, P), ancestor(P, D) can only come after. The parent/2 facts are always consumed before each recursive call. The facts are also finite, so the recursion always ends up at parent(A, D). It only changes the order of the direct/indirect descendants.
