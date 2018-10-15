# The MIR type-check

A key component of the borrow check is the [MIR type-check][].  This
check walks the MIR and does a complete "type check" -- the same kind
you might find in any other language. In the process of doing this
type-check, we also uncover the region constraints that apply to the
program.

[MIR type-check]: https://doc.rust-lang.org/nightly/nightly-rustc/rustc_mir/borrow_check/nll/type_check/index.html

As a simple example, imagine that we have a [MIR] statement that just
assigns one local to another, like so:

[MIR]: ../index.html

```
TMP0 = move TMP1
```

In this case, the MIR type checker would require that the type of
`TMP1` must be a subtype of the type of `TMP0`; the types of `TMP0`
and `TMP1` would be derived from the corresponding [`LocalDecl`
structs][LocalDecl] found in the MIR. In thi way, we check that the
types in the MIR are internally consistent.

[LocalDecl]: https://doc.rust-lang.org/nightly/nightly-rustc/rustc/mir/struct.LocalDecl.html

## Region erasure and the role of the MIR type-check

The MIR type-check is useful as a kind of "sanity check" for the MIR,
since it establishes that the MIR we produce doesn't try to do
ill-typed things. However, it plays a particularly important role for
the MIR borrow check: this is because the initial HIR-based type
checker does not (and cannot) establish that the code is "lifetime
safe".
