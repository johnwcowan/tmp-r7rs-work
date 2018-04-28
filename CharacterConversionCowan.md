## Character conversion

These routines are loosely based on the Gauche [gauche.charconv](http://practical-scheme.net/gauche/man/gauche-refe_78.html) package, which in turn is based on [GNU libiconv](https://www.gnu.org/savannah-checkouts/gnu/libiconv).

## Character encoding schemes

A *character encoding scheme* or *CES* is an algorithm for translating between a sequence of bytes and a sequence of characters and vice versa.  Converting bytes to characters is *decoding*; converting characters to bytes is *encoding*.  Typical CESes include ASCII, UTF-8, ISO-8859-1, and TIS-620; each one is named by a case-insensitive string.

`(ces-valid? `*ces*`)`

Returns `#t` if it is possible to convert between *ces* and the implementation-dependent internal CES of Scheme strings, and `#f` otherwise.

`(ces-conversion-supported? `*ces,,1,, ces,,2,,*`)`

Returns `#t` if it is possible to convert between *ces,,1,,* and *ces,,2,,* and `#f` otherwise.

`(ces-guess `*bytevector* [[|*hint* ]]`)`

Returns a CES which might be the correct encoding of the bytes in *bytevector*.  If *hint* is provided, it is a possible CES which gives a possibly helpful indication of the correct CES.  For example, given a hint of "UTF-8", this procedure might return "UTF-8" if *bytevector* contains well-formed UTF-8, or "ISO-8859-1" if it does not.

## String and bytevector conversion

`(string->bytevector `*ces string*`)`

Uses *ces* to encode *string* into a newly allocated bytevector, which is returned.

`(bytevector->string `*ces bytevector*`)`

Uses *ces* to decode *bytevector* into a newly allocated string, which is returned.

`(transcode-bytevector `*ces,,1,, ces,,2,, bytevector*`)`

Uses *ces,,1,,* to decode *bytevector* into a string, which is then encoded into a newly allocated bytevector using *ces,,2,,*.  However, the string need not actually be created.

## Conversion ports

All of these procedures accept an optional *size* argument, specifying the size of an internal conversion buffer in bytes.  It is an error if *size* is not a non-negative exact integer.  If *size* is 0, there is no internal buffer.  If *size* is omitted, the size of the buffer is implementation-dependent.

`(make-decoded-input-port `*binary-port ces* [[|*size* ]]`)`

Returns a textual input port which, when read from, reads bytes from *binary-port*, decodes them as characters using *ces*, and provides the characters to the reader.

`(make-encoded-output-port `*binary-port ces* [[|*size* ]]`)`

Returns a textual output port which, when characters are written to it, encodes them as characters using *ces*, and writes the bytes to *binary-port*.

`(make-encoded-binary-input-port `*textual-port ces* [[|*size* ]]`)`

Returns a binary input port which, when read from, reads characters from *textual-port*, encodes them as characters using *ces*, and provides the bytes to the reader.

`(make-decoded-binary-output-port `*textual-port ces* [[|*size* ]]`)`

Returns a binary output port which, when bytes are written to it, decodes them as characters using *ces*, and writes the characters to *textual-port*.

`(make-transcoded-binary-input-port `*binary-port ces,,1,, ces,,2,,* [[|*size* ]]`)`

Returns a binary input port which, when read from, reads bytes from *binary-port*, decodes them as characters using *ces,,1,,*, encodes the resulting characters as bytes using *ces,,2,,*, and provides the bytes to the reader.

`(make-transcoded-binary-output-port `*binary-port ces,,1,, ces,,2,,* [[|*size* ]]`)`

Returns a binary output port which, when bytes are written on it, decodes them as characters using *ces,,1,,*, encodes the resulting characters as bytes using *ces,,2,,*, and writes the bytes on *binary-port*.



