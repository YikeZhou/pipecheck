# Error 1

```
$ make

make -f Makefile.coq
make[1]: Entering directory '.../pipecheck'
COQC util2.v
File "./util2.v", line 305, characters 13-15:
Warning: Unused variable t' catches more than one case.
[unused-pattern-matching-variable,pattern-matching]
File "./util2.v", line 306, characters 7-9:
Warning: Unused variable n' catches more than one case.
[unused-pattern-matching-variable,pattern-matching]
File "./util2.v", line 384, characters 11-22:
Error: The reference nat_compare was not found in the current environment.

make[2]: *** [Makefile.coq:720: util2.vo] Error 1
make[1]: *** [Makefile.coq:343: all] Error 2
make[1]: Leaving directory '.../pipecheck'
make: *** [Makefile:6: coq] Error 2
```

## Solution

replace `nat_compare` with `nat_compare_alt` (see [this page](https://coq.inria.fr/library/Coq.Arith.Compare_dec.html#nat_compare_alt))

# Error 2

```
$ make

make -f Makefile.coq
make[1]: Entering directory '/mnt/c/Users/zyk/Documents/Project/coq/pipecheck'
COQDEP VFILES
COQC util2.v
File "./util2.v", line 305, characters 13-15:
Warning: Unused variable t' catches more than one case.
[unused-pattern-matching-variable,pattern-matching]
File "./util2.v", line 306, characters 7-9:
Warning: Unused variable n' catches more than one case.
[unused-pattern-matching-variable,pattern-matching]
File "./util2.v", line 452, characters 0-281:
Warning: Not a truly recursive fixpoint. [non-recursive,fixpoints]
COQC tree.v
File "./tree.v", line 67, characters 4-17:
Error: The constructor GraphTreeOr (in type GraphTree) expects 2 arguments.

make[2]: *** [Makefile.coq:720: tree.vo] Error 1
make[1]: *** [Makefile.coq:343: all] Error 2
make[1]: Leaving directory '/mnt/c/Users/zyk/Documents/Project/coq/pipecheck'
make: *** [Makefile:6: coq] Error 2
```

## Solution

add `_` so that Coq will guess possible type for GraphTree