# Type-Directed Canonical Restriction with Two-Level Type Theory

```agda
@extern set_random_tick_speed : Int → Unit
```

```agda
⟦_⟧ : Unit → Command
```

```agda
⟦set_random_tick_speed 0⟧ = /gamerule randomTickSpeed 0
⟦set_random_tick_speed 1⟧ = /gamerule randomTickSpeed 1
                          ⋮
```

```agda
⟦set_random_tick_speed (1 + 1)⟧ = /gamerule randomTickSpeed 2
```

```agda
⟦set_random_tick_speed x⟧       = /gamerule randomTickSpeed ???
⟦set_random_tick_speed (x + 1)⟧ = /gamerule randomTickSpeed ???
```

- Quotable
- Spliceable

```agda
↑A₀ = A₁

⇒ : ↑(A₀ →₀ B₀) →₁ (↑A₀ →₁ ↑B₀)
⇒ = λ₁ f₁ ↦ λ₁ a₁ ↦ '($f₁ $a₁)

⇐ : (↑A₀ →₁ ↑B₀) →₁ ↑(A₀ →₀ B₀)
⇐ = λ₁ f₁ ↦ '(λ₀ a₀ ↦ $(f₁ 'a₀))
--                         ^^^

id₀ : A₀ →₀ A₀
id₀ = λ₀ x₀ ↦ x₀

id₁ : A₁ →₁ A₁
id₁ = ⇒ 'id₀
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
@extern set_random_tick_speed : Int₁ →₁ Unit₁
```
