- Feature Name: `lazy_static!`
- Start Date: 2018-06-29
- RFC PR:
- Rust Issue:

# Summary
[summary]: #summary

The `lazy_static!` macro is widely used and stable.  It provides an efficient
method to create heap allocated values stored in a known location.  Rust
does not provide a native way to evaluate an expression and store the
output as a global variable.  This RFC proposes moving `lazy_static!` to
`std`.

# Motivation
[motivation]: #motivation

[RFC 1242]: http://rust-lang.github.io/rfcs/1242-rust-lang-crates.html#the-lifecycle-of-a-rust-lang-crate

[What should go into the standard library?]: https://internals.rust-lang.org/t/what-should-go-into-the-standard-library/2158

`lazy_static` crate currently is within the rust-lang-nursery organization.  There are 1100 (9% of all) crates that depend on `lazy_static`.  The features
for `lazy_static` are complete, and the macro should enter a phase marked
with stability.  As new versions of Rust are released, `lazy_static!` should
continue to work without releasing a new crate version.

According to [RFC 1242], `lazy_static` has reached the point where a decision
needs to be made about moving the crate to rust-lang.  In [What should go into the standard library?]
a few points are made about `std` inclusion (paraphrased):
 1. Curation - indicates quality, scrutiny
 2. Ubiquitously used - De Facto implementation of heap allocated statics
 3. Completeness - The API should be stable

 Additionally, `std::thread_local!` macro performs an abstraction similar
 to `lazy_static` so there is a precedence for having this feature.

# Guide-level explanation
[guide-level-explanation]: #guide-level-explanation

`lazy_static!` is a macro that allows expressions assigned to a static.
In Rust today, there is no way to assign the output of an expression to a
static.  Therefore, heap allocations are not possible for `static`.

Lazy refers to the evaluation of the expression taking place at the first
runtime call to the `static`.  Subsequent calls will return same value,
without re-evaluating the expression.

# Reference-level explanation
[reference-level-explanation]: #reference-level-explanation

`lazy_static` would continue to exist as a crate.  This crate would provide
the same functionality `lazy_static!` in `std` implements for both `no_std`
feature and for versions of Rust that do not have `lazy_static!` in `std`.

# Drawbacks
[drawbacks]: #drawbacks

It is a core belief that Rust desires `std` to be small.  `lazy_static`
crate is already in use providing a broader implementation than could be
provided by adding it to `std`, specifically older Rust versions and
`no_std` binaries.  

`lazy_static` crate will need an update to prevent conflicting
implementations. Libraries that use `lazy_static` will need to update
their dependencies to support the latest Rust version.

Significant changes to the `lazy_static` implementation will require
longer development cycles to be released to stable.

# Rationale and alternatives
[alternatives]: #alternatives

`lazy_static` needs a home.  This crate has graduated rust-lang-nursery.
This crate can join `libc` and `regex` in rust-lang proper or follow
recent `bitflags` course, assigning an organization around maintainership.

Allowing heap allocations for statics or compile time generated statics
are also alternatives, but have not experienced the thorough testing
`lazy_static` has endured.


# Unresolved questions
[unresolved]: #unresolved-questions

- Can `lazy_static` live in `std::core` to stabilize `no_std`?
- Will the crate be required to support `no_std` implementations?
- What will happen to the crate if the RFC is accepted?
