These are things to consider making mandatory in the large language that are optional in the small language or in SRFIs that form part of the large language.  See [wiki:WG2Dockets] for other dockets.

Full numeric tower support:  arbitrary rationals and inexact complex numbers

Forbid mutation of literals

Exact complex numbers: not required

Ephemeron-based hash tables

IEEE 64-bit binary inexact numbers

Multiple precisions

Safety

Return exact results on exact arguments whenever possible (e.g. `(sqrt 4)`)

Full Unicode support:

  * all Unicode characters as characters
  * all Unicode characters except perhaps U+0000 in strings
  * all allowed Unicode characters in identifiers

Unicode identifiers

Fixnums must be at least 24 bits signed

Signal an error on all [ItIsAnError](ItIsAnError.md) situations

Provide all R7RS-small libraries:

  * case-lambda
  * cxr
  * eval
  * file
  * inexact
  * lazy
  * load
  * process-context
  * read
  * repl
  * time
  * write

Raise exceptions on all errors

Forcing a non-promise returns the object

Implicit forcing of promises by primitives

Require Unicodely-correct (but not AI-correct) downcasing of Σ.

Support multiple inexact-number precisions

Input/output ports

