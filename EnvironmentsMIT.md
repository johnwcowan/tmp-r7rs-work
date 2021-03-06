This SRFI provides the ability to create and control top-level
Scheme environments, which are used in R[567]RS Scheme
to give meaning to variables and syntactic keywords
that are referred to in contexts where they have not been lexically bound.
These environments can be passed to `eval` as a second argument, but otherwise
cannot be controlled or manipulated.

In R5RS there were only three such environments; R6RS and R7RS added the ability
to create novel environments based on a set of import specifiers.  The environments
of this SRFI are built on top of those environments, but add the ability to
create, obtain, and destroy bindings and to create either fully mutable
or immutable environments, as well as an inheritance relationship
between environments that makes them a forest of trees.

## Constructors

`(interaction-environment)`   [R5RS, R6RS, R7RS]

Returns a unique fully mutable environment containing an implementation-specified
set of bindings.  On R6RS systems, at least the `(rnrs base`) bindings must be
included; on R7RS systems, at least the `(scheme base)` bindings must be included.
The intention is that this environment is the one used to
evaluate expressions and definitions entered to the system REPL.

`(scheme-report-environment `*version*`)`  [R5RS, R6RS, R7RS]

If *version* is the exact integer 5, returns an environment containing the bindings
of the R5RS in R5RS systems, of the `(rnrs r5rs)` library in R6RS systems,
or of the `(scheme r5rs)` library in R7RS systems.
These are almost but not quite the same (e.g. `transcript-on` and
`transcript-off` are not part of the R6RS or R7RS libraries).
The returned environment may be mutable or immutable.

Implementations may also support other values of *version*,
in which case they return an environment
containing bindings corresponding to the specified version
of the report. If version is neither 5 nor another value
supported by the implementation, an error is signaled.

`(null-environment `*version*`)`  [R5RS, R6RS, R7RS]

This procedure behaves the same as `scheme-report-environment`,
except that only syntactic keywords are bound and not variables.

`(environment `*list* ...`)`  [R6RS, R7RS]

This procedure returns the environment that
results by starting with an empty environment and then
importing each list, considered as an import set, into it.
In R6RS systems, the 
bindings of the resulting environment
are immutable; R7RS additionally requires that the environment
itself is immutable (no bindings may be added or removed).

`(make-environment `*environment*`)`

Creates and returns a newly allocated environment that inherits
from *environment*.  If a lookup in this environment does not
find a suitable binding, *environment* (known as the parent
environment) will be searched next.  The new environment is mutable.

`(make-empty-environment)`

Creates and returns a newly allocated environment with no bindings.
The new environment is mutable.

## Predicates

`(environment? `*obj*`)`

Returns `#t` if *obj* is an environment in the sense of this SRFI,
and `#f` on all other standard Scheme objects.  However, if the
implementation reifies local lexical environments, which this SRFI
does not require, `environment?` may return `#t` on them as well.

`(environment-has-parent? `*env*`)`

Returns `#t` if *env* has a parent environment and
`#f` if it does not.  It is unspecified whether the interaction
environment, as well as environments returned by
`scheme-report-environment` and `null-environment`, have parents or not.
Environments returned by `environment` and `make-empty-environment`
do not have parents.

`(environment-bound? `*environment symbol*`)`

Returns `#t` if *symbol* is bound in *env*, and `#f` otherwise.

`(environment-assigned? `*environment symbol*`)`

Returns `#t` if *symbol* is not only bound in *env* but
assigned to a value, and `#f` otherwise.

Such unassigned symbols cannot be created by ordinary Scheme operations.
However, a Scheme implementation may treat all
possible symbols as bound but not necessarily assigned in the
interaction environment.

`(environment-assignable? `*env symbol*`)`

Returns `#t` if *symbol* is assignable in *env*
(it is bound and can be modified) and `#f` otherwise.

`(environment-definable? `*env symbol*`)`

Returns `#t` if *symbol* is definable in *env*.
In other words, *symbol* is either bound and can be assigned
or is unbound and can be bound and assigned in the environment).
Otherwise, `#f` is returned.

## Accessors

`(environment-parent `*env*`)`

Returns the parent environment of *env*; it is an error if
*env* has no parent (see `environment-has-parent?`).

`(environment-bound-names `*env*`)`

Returns a list of all the names bound in *env*, excluding those
bound only in the ancestors of *env*.  However, if the environment
treats all symbols as bound, it need not return every possible
synbol.  It is an error to mutate this list.

`(environment-bindings `*env*`)`

Returns a list of the bindings in *env*, excluding those
bound only in the ancestors of *env*.  However, if the environment
treats all symbols as bound, it need not return every possible
synbol.  It is an error to mutate this list.

Each element of the list is itself a list, whose first element
is the bound symbol and whose second element, if there is one,
is the assigned value.

`(environment-reference-type `*env symbol*`)`

Returns a symbol, one of `unbound`, `unassigned`, `macro`, or `normal`
that represents the status of *symbol* in *env* and its ancestors.
The difference between `macro` and `normal` is that `macro` means that
*symbol* is a syntactic keyword, whereas `normal` means it represents a variable.

`(environment-lookup `*env symbol*`)`

Returns the assigned value of `symbol` in *env* or its ancestors.  It is an
error if *symbol* is not a normal symbol in the environment
in the sense of `environment-reference-type`.

`(environment-lookup-macro `*env symbol*`)`

Returns the assigned value of `symbol` in *env* or its ancestors.  It is an error
if *symbol* is not a syntactic keyword.  The assigned value is a syntax transformer
whose implementation is system-dependent.

## Mutators

Except as noted, it is an error to invoke any of the following procedures
on an immutable environment.  They all return an unspecified value.

`(environment-freeze! `*env*`)`

Makes *env* an immutable environment; does nothing if *env* is already immutable.
It is an error to add or remove bindings or to change assigned values in an
environment after it has been frozen.

`(environment-assign! `*env symbol value*`)`

Assigns *value* to *symbol* in *env*, making *symbol* a normal symbol in the
sense of `environment-reference-type`.  It is an error if *symbol* is not
assignable in *env*.

`(environment-define `*env symbol value*`)`

Defines *symbol* as *value* in *env*, making *symbol* a normal symbol in the
sense of `environment-reference-type`.  It is an error if *symbol* is not
definable in *env*.

`(environment-define-macro `*env symbol value*`)`

Defines *symbol* as *value* in *env*, making *symbol* a syntactic keyword.
No check is made to see if *value* is really a syntax transformer.
It is an error if *symbol* is not definable in *env*.

`(environment-unbind `*env symbol*`)`

Causes *symbol* to be unbound in *env*.

## Implementation

This SRFI is inherently not portable.  MIT Scheme provides a nearly complete
implementation of it, with the following exceptions:

  *  MIT Scheme does not have libraries in the sense of R6RS or R7RS, so
     the `environment` procedure is not supported.
     
  *  Immutable environments are not supported, so `environment-freeze!` can be
     implemented as a procedure that does nothing.
     
  *  The `make-environment` procedure is called `make-top-level-environment`.
  
  *  The `make-empty-environment` procedure is called `make-root-top-level-environment`.
  
  *  The `environment-unbind` procedure is called `unbind-variable`, although it
     can unbind any name, not just a variable.
     
  *  The `interaction-procedure` environment can be simplemented as a procedure that
     returns the value of the variable `user-initial-environment`.
     
  *  The `scheme-report-environment` and `null-environment` procedures can be
     implemented as follows:
     
     1. Check the *version* argument and make sure its value is 5; raise an exception otherwise.
     
     2. Create a fresh environment with the `make-root-top-level-environment` procedure.
     
     3. Populate the new environment with the appropriate R5RS bindings
        using the `link-variables` procedure, which accepts four arguments:
        the source environment, which is `system-global-environment`, the bound
        symbol, the new environment, and the symbol to be bound (always the same
        as the second argument in this use).  However, the names `interaction-environment`,
        `scheme-report-environment`, and `null-environment` must be linked from
        `initial-user-environment` rather than `system-global-environment`.
        
     4. Return the new environment.
	 