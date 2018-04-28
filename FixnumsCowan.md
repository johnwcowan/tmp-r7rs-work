## Abstract


Fixnums are an implementation-defined subset of the exact integers.
Every implementation of this SRFI must define its fixnum range as a closed
interval [2^*w*-1^-1](-2^*w*-1^,),
where *w* is an integer greater than or equal to 24.  Every
mathematical integer within an implementation's fixnum range must
correspond to an exact integer that is representable within the
implementation.
A fixnum is an exact integer whose value lies within this
fixnum range.

## Rationale

Fixnum arithmetic is already supported by many systems, mainly for efficiency. Standardizing fixnum arithmetic increases the portability of code that uses it. Standardizing the range of fixnums would make fixnum operations inefficient on some systems, which would defeat their purpose. Therefore, this SRFI specifies some of the semantics of fixnums, but makes the range implementation-dependent.

Existing implementations employ different implementation strategies for fixnums: Some implement the model specified by R6RS (overflows cause exceptions), some implement modular arithmetic (overflows “wrap around”), and others do not handle arithmetic overflows at all.  In programs that use fixnums instead of generic arithmetic, overflows are typically programming mistakes.

## Specification

Fixnum operations perform integer arithmetic on their fixnum
arguments.  If any argument is not a fixnum, or if the mathematical result
is not representable as a fixnum, it is an error: this is known as the
*fixnum rule*.  In particular, this means
that fixnum operations may return a mathematically incorrect fixnum in these
situations without raising an error.  Exceptions to the fixnum rule
are noted below.

This SRFI uses *fx*, *fx,,1,,*, *fx,,2,,*, etc., as parameter
names for fixnum arguments.  Except as noted, the name of fixnum procedures begin with
the letters `fx`.  In most cases they correspond to an R7RS-small or
[DivisionRiastradh](DivisionRiastradh.md) or [BitwiseCowan](BitwiseCowan.md) operation on general integers.

### Constants

`fx-width`

Bound to the value *w* that specifies the implementation-defined range.
(R6RS `fixnum-width` is a procedure that always returns this value.)

`fx-greatest`

Bound to the value 2^*w*-1^-1, the largest representable fixnum.
(R6RS `greatest-fixnum` is a procedure that always returns this value.)

`fx-least`

Bound to the value -2^*w*-1^, the smallest representable fixnum.
(R6RS `least-fixnum` is a procedure that always returns this value.)

### Predicate

`(fixnum? `*obj*`)`

Returns `#t` if *obj* is an exact integer within the fixnum range,
and `#f` otherwise.

### Basic arithmetic

The following procedures are the fixnum counterparts of procedures from R7RS-small:
```
fxzero? fxpositive? fxnegative? fxodd? fxeven?
fx= fx< fx> fx<= fx>=
fxmax fxmin
fx+ fx- fx*
fxabs fxsquare fxsqrt fxexpt
```

Except for the effects of the fixnum rule, the `fx` versions
have the same arguments and semantics as their generic counterparts,
with the following additional modifications:

* The procedures `fx+` and `fx*` accept exactly two arguments.

* The procedure `fx-` accepts either one or two arguments.

* The `fxsqrt` procedure is the counterpart of `exact-integer-sqrt` rather than `sqrt`.

Note that in accordance with the fixnum rule the procedure `fxabs` has undefined
results when applied to `fx-least`.

### Arithmetic with carry

`(fx+/carry `*fx,,1,, fx,,2,, fx,,3,,*`)‌‌`

Returns the two fixnum results of the following computation:

```
(let* ((s (+ fx1 fx2 fx3))
       (s0 (balanced-remainder s (expt 2 (fixnum-width))))
       (s1 (balanced-quotient s (expt 2 (fixnum-width)))))
  (values s0 s1))
```

`(fx-/carry `*fx,,1,, fx,,2,, fx,,3,,*`)‌‌`

Returns the two fixnum results of the following computation:

```
(let* ((d (- fx1 fx2 fx3))
       (d0 (balanced-remainder d (expt 2 (fixnum-width))))
       (d1 (balanced-quotient d (expt 2 (fixnum-width)))))
  (values d0 d1))
```

`(fx*/carry `*fx,,1,, fx,,2,, fx,,3,,*`)‌‌`

Returns the two fixnum results of the following computation:

```
(let* ((s (+ (* fx1, fx1)) fx3))
       (s0 (balanced-remainder s (expt 2 (fixnum-width))))
       (s1 (balanced-quotient s (expt 2 (fixnum-width)))))
  (values s0 s1))
```


### Integer division

The following procedures are the fixnum counterparts of procedures
from [SRFI 141](http://srfi.schemers.org/srfi-141/srfi-141.html):
```
fxfloor/ fxfloor-quotient fxfloor-remainder
fxceiling/ fxceiling-quotient fxceiling-remainder
fxtruncate/ fxtruncate-quotient fxtruncate-remainder
fxround/ fxround-quotient fxround-remainder
fxeuclidean/ fxeuclidean-quotient fxeuclidean-remainder
fxbalanced/ fxbalanced-quotient fxbalanced-remainder
```
Except for the effects of the fixnum rule, the `fx` versions
have the same arguments and semantics as their generic counterparts.

### Bitwise operations

The following procedures are the fixnum counterparts of procedures
from [SRFI 141](http://srfi.schemers.org/srfi-141/srfi-141.html):
```
fxnot
fxand   fxior   fxxor   fxeqv
fxnand  fxnor 
fxandc1 fxandc2 fxorc1  fxorc2 

farithmetic-shift fxbit-count fxinteger-length

fxif 
fxbit-set? fxcopy-bit fxbit-swap
fxany-bit-set? fxevery-bit-set?
fxfirst-set-bit

fxbit-field fxbit-field-any? fxbit-field-every?
fxbit-field-clear fxbit-field-set
fxbit-field-replace  fbit-field-replace-same
fxbit-field-rotate fxbit-field-reverse
fxbit-field-append

fixnum->list list->fixnum
fixnum->vector vector->fixnum
fxbits
fxfold fxfor-each fxunfold
```
Except for the effects of the fixnum rule, the `fx` versions
have the same arguments and semantics as their generic counterparts,
with the following additional modifications:

* The prefix `bitwise-` in the SRFI 141 functions is dropped for brevity and compatibility.

* Despite the fixnum rule, the `fxarithmetic-shift` procedure produces a defined result on all fixnums by discarding any higher-order bits that do not fit into the fixnum width.

* The `fixnum->list` and `list->fixnum` procedures correspond to the `integer->list` and `list->integer` procedures respectively, and the same for their vector analogues.

The following additional bitwise procedure is provided:

`(fxlogical-shift `*i count*`)`

When left shifting (*count* > 0), returns the same result
as `fxarithmetic-shift`.  When right shifting,
always inserts 0 bits at the most significant end
rather than copies of the sign bit.

The result of a logical shift depends on the value of `fx-width`.
This means that if `fx-width` were 8 (which this SRFI does not permit),
`(fxlogical-shift -8 -1)` would be `#x74`, or 116, rather than -4.

