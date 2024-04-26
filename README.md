# formic.id

[notes](notes.md)

## Barely Even Structured Text (BEST)

Spaces are separators outside of double quotes and escape sequences.

Strings are prefixed by `'` or delimited by `"`.

The rest is symbols. They can be prefixed by `\'`, or delimited by `\"` and `"`.

Quoting is preserved by parsers and printers, and meaningful in some circumstances (eg in [STOR](#simple-textual-object-representation-stor), `[` starts a vector but `\'[` does not).

`\` can be used to escape:
- `'` single quote
- `"` double quote
- ` ` space
- `\` backslash
- `n` newline
- `r` carriage return
- `t` tab
- `f` form feed
- `v` vertical tab
- `b` backspace
- `[0-9A-F][0-9A-F]` arbitrary byte in hex
- `u[0-9A-F][0-9A-F][0-9A-F][0-9A-F]` unicode code point in hex (represented in UTF-8)

## Simple Textual Object Representation (STOR)

Builds on [BEST](#barely-even-structured-text-best).

Treats some symbols specially.

- Unquoted `[` starts a vector, a matching unquoted `]` ends it; there is no special separator between elements
- Unquoted `(` starts an executable vector, a matching unquoted `)` ends it; there is no special separator between elements
- Unquoted `{` starts a dict, a matching unquoted `}` ends it; there is no special separator between key and value or between pairs
- Unquoted `#` can be followed by:
  - `u` undefined
  - `n` null
  - `f` false
  - `t` true
  - `e` for an elided cycle
  - `c` comment following (can take any form)
  - `b` binary following (to indicate arbitrary bytes, distinct from a string)
  - `B` base64url-encoded binary following
  - `f64` 64-bit float following
  - `f32` 32-bit float following
  - `f16` 16-bit float following
  - `i64` 64-bit signed integer following
  - `i32` 32-bit signed integer following
  - `i16` 16-bit signed integer following
  - `i8` 8-bit signed integer following
  - `u64` 64-bit unsigned integer following
  - `u32` 32-bit unsigned integer following
  - `u16` 16-bit unsigned integer following
  - `u8` 8-bit unsigned integer following
  - `[0-9]*` CBOR-style tag following
- Unquoted `%i` where `i` is an integer is `#6 %u8 i` (a shorthand for STORM primitives)

Numbers can take the forms `0` `+3.14` `6.626068e-34` `+299_792_458` `0xdeadbeef` `+inf`, `-inf`, `nan`. Whatever Nim parses today (to be better specified later).

## Binary Equal Alternative to Structured Text (BEAST)

Bijection to [BEST](#barely-even-structured-text-best), a subset of [CBOR](https://cbor.io/) data streams where every item is a byte string, sometimes but not always tagged.

- Untagged corresponds to a unquoted symbol
- Tag 7 corresponds to single-quoted symbol
- Tag 8 corresponds to double-quoted symbol
- Tag 9 corresponds to a single-quoted string
- Tag 10 corresponds to a double-quoted string

More compact than BEST for large binary-heavy data, primarily intended to avoid writing parsers and printers for yet another language in yet another language.
