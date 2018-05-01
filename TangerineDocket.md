This is an early draft of proposals for the Tangerine Edition of R7RS-large.
This edition consists of proposals excluded from the Red Edition, and ready substantially
before the rest of the Orange Edition, hence its color name.
For other dockets, see [WG2Dockets](WG2Dockets.md).

**Data structures**

Strings: [SRFI 13](http://srfi.schemers.org/srfi-13/srfi-13.html) (comprehensive),
[SRFI 130](http://srfi.schemers.org/srfi-130/srfi-130.html) (cursor-based),
[SRFI 152](http://srfi.schemers.org/srfi-152/srfi-152.html) (basic)

Ordered sets and bags:  [SRFI 153](http://srfi.schemers.org/srfi-153/srfi-153.html)

Mappings: [SRFI 146](http://srfi.schemers.org/srfi-146/srfi-146.html)

Regular expressions: [SRFI 115](http://srfi.schemers.org/srfi-115/srfi-115.html)

**Numeric types and operations**

Integer division: [SRFI 141](http://srfi.schemers.org/srfi-141/srfi-141.html)

Bitwise integer operations:  [SRFI 151](http://srfi.schemers.org/srfi-151/srfi-151.html) (superseding SRFI 142), 
[SRFI 60](http://srfi.schemers.org/srfi-60/srfi-60.html), 
[R6RS](http://www.r6rs.org/final/html/r6rs-lib/r6rs-lib-Z-H-12.html#node_sec_11.4).

Fixnums:  [SRFI 143](http://srfi.schemers.org/srfi-143/srfi-143.html),
[R6RS](http://www.r6rs.org/final/html/r6rs-lib/r6rs-lib-Z-H-12.html#node_sec_11.2)

Flonums:  [SRFI 144](http://srfi.schemers.org/srfi-144/srfi-1443.html),
[R6RS](http://www.r6rs.org/final/html/r6rs-lib/r6rs-lib-Z-H-12.html#node_sec_11.3)

**Numeric and semi-numeric data structures**

Numeric vectors:  [R6RS](http://www.r6rs.org/final/html/r6rs-lib/r6rs-lib-Z-H-3.html#node_chap_2,),
[SRFI 4](http://srfi.schemers.org/srfi-4/srfi-4.html),
[SRFI 74](http://srfi.schemers.org/srfi-74/srfi-74.html), 
[NumericVectorsCowan](NumericVectorsCowan.md).

Multidimensional arrays: [SRFI 122](http://srfi.schemers.org/srfi-122/srfi-122.html) .

**Formatting**

`number->string` and `string->number` with non-ASCII decimal digits: [NumberStringUnicode](NumberStringUnicode.md)

Formatting: [SRFI 159](http://srfi.schemers.org/srfi-159/srfi-159.html) (combinators),
[SRFI 48](http://srfi.schemers.org/srfi-48/srfi-48.html) (string templates).