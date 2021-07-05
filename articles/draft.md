# Type-Directed Canonical Restriction with Two-Level Type Theory

## Introduction

Consider the following function:

```agda
@external set_random_tick_speed : Int → Unit
```

This is an external function which update the gamerule `randomTickSpeed` to a given value.
The compilation looks like:

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

One solution is to evaluate the annotated argument and accept it if and only if its value is in a [*canonical form*](https://ncatlab.org/nlab/show/canonical+form).
In the cases above, both `x` and `(x + 1)` are in a *neutral form*, whose computation is blocked by a variable, and thus rejected.

However, this solution seems to be ad-hoc because well-typed programs are rejected depending on the extra layer of evaluation results.
In this article, I will explore how to enforce the canonical restriction in a *type-directed* way using the *two-level type theory* and *staging*.

---

- Quotable
    - Primitive types are not quotable.
- Spliceable
    - All types are spliceable?

```agda
↑A₀ = A₁

⇒ : ↑(A₀ →₀ B₀) →₁ (↑A₀ →₁ ↑B₀)
⇒ = λ₁ f₁ ↦ λ₁ a₁ ↦ '($f₁ $a₁)
--                  ^^^^^^^^^^ quotation of B₀
--
-- A function of type (A₁ →₁ B₁) is derivable from a function of type (A₀ →₀ B₀) iff B is quotable.

⇐ : (↑A₀ →₁ ↑B₀) →₁ ↑(A₀ →₀ B₀)
⇐ = λ₁ f₁ ↦ '(λ₀ a₀ ↦ $(f₁ 'a₀))
--                         ^^^ quotation of A₀
--
-- A function of type (A₀ →₀ B₀) is derivable from a function of type (A₁ →₁ B₁) iff A is quotable.

-- Functions of type (A₀ →₀ B₀) and (A₁ →₁ B₁) are not derivable from each other if neither A nor B is quotable.
```

```agda
┌─Type──────────┐
│ ┌─Int─┐ A     │
│ │  0  │ B     │
│ │  1  │ A → B │
│ │  ⋮  │       │
│ └─────┘       │
└───────────────┘
```

```agda
┌─Type₀─────────────┐ ┌─Type₁─────────────┐
│ ┌─Int₀─┐ A₀       │ │ ┌─Int₁─┐ A₁       │
│ │  0₀  │ B₀       │ │ │  0₁  │ B₁       │
│ │  1₀  │ A₀ →₀ B₀ │ │ │  1₁  │ A₁ →₁ B₁ │
│ │  ⋮   │          │ │ │  ⋮   │          │
│ └──────┘          │ │ └──────┘          │
└───────────────────┘ └───────────────────┘
```

```agda
@external set_random_tick_speed : Int₁ →₁ Unit₁
```

## References

1. <a id="user-content-1"></a>[András Kovács. **Using Two-Level Type Theory for Staged Compilation**](https://github.com/AndrasKovacs/staged/blob/main/types2021/abstract.pdf)
