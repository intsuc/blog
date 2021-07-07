# A Type System for Canonical Restriction

## Introduction

Consider the following function:

```agda
@external set_random_tick_speed : Int → IO Unit
```

This is an external function which update the gamerule `randomTickSpeed` to a given value.
The compilation roughly looks like:

```agda
⟦set_random_tick_speed 0⟧ = /gamerule randomTickSpeed 0
⟦set_random_tick_speed 1⟧ = /gamerule randomTickSpeed 1
⋮
```

What if the argument is not just an integer constant?
In that case, we can just evaluate the argument:

```agda
  ⟦set_random_tick_speed (1 + 1)⟧
= ⟦set_random_tick_speed 2⟧
= /gamerule randomTickSpeed 2
```

Then, what if the argument has free variables?

```agda
x : Int ⊢ ⟦set_random_tick_speed x⟧       = /gamerule randomTickSpeed ???
x : Int ⊢ ⟦set_random_tick_speed (x + 1)⟧ = /gamerule randomTickSpeed ???
```

Now we are stuck.
We do not know what goes in `???` since `/gamerule randomTickSpeed <value>` requires an integer constant for `<value>`.
This is what I call the *canonical restriction*.

One solution is to evaluate the argument and accept it if and only if its value is in a [*canonical form*](https://ncatlab.org/nlab/show/canonical+form).
In the cases above, both `x` and `(x + 1)` are in a *neutral form*, whose computation is blocked by a variable, and thus rejected.

However, this solution is ad-hoc for the following two reasons:

- Well-typed programs are rejected depending on the syntax forms of evaluated expressions.
- Difficult to know whether an expression will be evaluated at compile-time or not.

In this article, I will explore a type system to enforce the canonical restriction, so that programs that violate the restriction become ill-typed.
This article is heavily influenced by [[1](#1)].

## Two-Level Universes

Let us create two universes.
One is a run-time universe `Type₀` and the other is a compile-time universe `Type₁`.
The terms belonging to the compile-time universe are completely evaluated at compile-time by *staging* [[2](#2)].
Our universes look like:

```agda
┌─Type₀────────────┐ ┌─Type₁────────────┐
│ ┌─Int₀─┐ A₀      │ │ ┌─Int₁─┐ A₁      │
│ │  0₀  │ B₀      │ │ │  0₁  │ B₁      │
│ │  1₀  │ A₀ → B₀ │ │ │  1₁  │ A₁ ⇒ B₁ │
│ │  ⋮   │ ⋮       │ │ │  ⋮   │ ⋮       │
│ └──────┘         │ │ └──────┘         │
└──────────────────┘ └──────────────────┘
```

where `→` and `⇒` are function type formers in each universe.

As a starter, consider the following simple example:

```agda
let id : Int₁ ⇒ Int₁ = λ₁ x₁. x₁ in
$(id 0₁) : Int₀
```

`id` is a compile-time function that expects a compile-time integer.
`id` is applied to `0₁`, which is an integer constant in the compile-time universe.
Then, the result `0₁` is *spliced* (`$`) into `0₀`, which is an integer constant in the run-time universe.

Note that we do not allow *quote*, which lifts an expression in the run-time universe into one in the compile-time universe.
If we allow this, we need to consider *cross-stage persistence* [[2](#2)].
In our case, because we cannot persist any variables, we will just perform *canonical form checking* again.

> TODO

## References

1. <a id="user-content-1"></a>[András Kovács. **Using Two-Level Type Theory for Staged Compilation**](https://github.com/AndrasKovacs/staged/blob/main/types2021/abstract.pdf)
2. <a id="user-content-2"></a>[Walid Taha and Tim Sheard. 1997. **Multi-stage programming with explicit annotations**. SIGPLAN Not. 32, 12 (Dec. 1997), 203–217.](https://doi.org/10.1145/258994.259019)
