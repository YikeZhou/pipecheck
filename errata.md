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
make[1]: Entering directory '.../pipecheck'
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
make[1]: Leaving directory '.../pipecheck'
make: *** [Makefile:6: coq] Error 2
```

## Solution

add `_` so that Coq will guess possible type for GraphTree

# Error 3

```
$ make

make -f Makefile.coq
make[1]: Entering directory '.../pipecheck'
COQDEP VFILES
COQC tree.v
COQC adjacency.v
File "./adjacency.v", line 187, characters 19-60:
Error:
No interpretation for string "PathBacktrace' iteration limit exceeded".

make[2]: *** [Makefile.coq:720: adjacency.vo] Error 1
make[1]: *** [Makefile.coq:343: all] Error 2
make[1]: Leaving directory '.../pipecheck'
make: *** [Makefile:6: coq] Error 2
```

## Solution

Add following codes:

```
Require Import String.
Open Scope string_scope.
```

# Import Error

When stepping, Coq complains that

```
The file foo.vo contains library Top.foo and not library foo
```

See this Stack Overflow [page](https://stackoverflow.com/questions/61561014/comparable-vo-contains-library-top-comparable-and-not-library-comparable)

# Error 4

```
$ make

make -f Makefile.coq
make[1]: Entering directory '.../pipecheck'
COQC execution.v
File "./execution.v", line 157, characters 0-133:
Warning: Not a truly recursive fixpoint. [non-recursive,fixpoints]
File "./execution.v", line 178, characters 0-123:
Warning: Not a truly recursive fixpoint. [non-recursive,fixpoints]
File "./execution.v", line 184, characters 0-349:
Warning: Not a truly recursive fixpoint. [non-recursive,fixpoints]
File "./execution.v", line 184, characters 0-349:
Error: Cannot guess decreasing argument of fix.

make[2]: *** [Makefile.coq:720: execution.vo] Error 1
make[1]: *** [Makefile.coq:343: all] Error 2
make[1]: Leaving directory '.../pipecheck'
make: *** [Makefile:6: coq] Error 2
```

## Solution

buggy Fixpoint definition:

```Coq
Fixpoint RFPerformPairs
  (src dst : PathOption)
  : list (location * location) :=
  let src_core := proc (iiid (evt src)) in
  let dst_core := proc (iiid (evt dst)) in
  let src_perf_stages := PerformStagesWRTCore dst_core src in
  let dst_perf_stages := VisibleStagesWRTCore src_core dst in
  CartesianProductPairs src_perf_stages dst_perf_stages.
```

change `Fixpoint` into `Definition`

# Error 5

```
$ make

make -f Makefile.coq
make[1]: Entering directory '.../pipecheck'
COQC risc.v
File "./risc.v", line 68, characters 51-64:
Error: No interpretation for string "StoreBuffer".

make[2]: *** [Makefile.coq:720: risc.vo] Error 1
make[1]: *** [Makefile.coq:343: all] Error 2
make[1]: Leaving directory '.../pipecheck'
make: *** [Makefile:6: coq] Error 2
```

## Solution

see [Error 3](#error-3)

# Error 6

```
$ make

make -f Makefile.coq
make[1]: Entering directory '.../pipecheck'
COQC risc.v
File "./risc.v", line 99, characters 2-69:
Error:
In environment
RISC_AllStages : forall n : nat, ?T
n : nat
The term
 "fold_left (app (A:=Stage)) (map (RISC_PipelineStages n) [0 ... n - 1]) []"
has type "list Stage" while it is expected to have type 
"string".

make[2]: *** [Makefile.coq:720: risc.vo] Error 1
make[1]: *** [Makefile.coq:343: all] Error 2
make[1]: Leaving directory '.../pipecheck'
make: *** [Makefile:6: coq] Error 2
```

while interpreter complains

```
Syntax error: [term level 200] expected after '...' (in [term]).
```

## Solution

notation "++" appears in `list_scope` and `string_scope`

add following codes before definition

```
Close Scope string_scope.
Open Scope list_scope.
```

similar problems in other pipeline specification files