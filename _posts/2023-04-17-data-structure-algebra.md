---
published: true
title: Data structures and algebra
---

I think one of the most magical things in all of computer science is that we can reason with data structures algebraically. Many data structures can be described as a sum and product of types:

```haskell
data Maybe a = Just a | Nothing
data List a = Nil | Cons a (List a)
```

Why are they called sums and products? A major reason is that the usual laws that we expect from sums and products hold for data structures as well, even if only up to isomorphism. Let $0$ denote the `Void` type (the initial object), $1$ denote a singleton type `()` (the terminal object), and we will assume that all type variables have already been instantiated so that each term has kind `*`. Then, for example, the types

```haskell
data Foo a = Foo a (Either a a)
data Bar a = Bar1 a a | Bar2 a a
```

are isomorphic:

```haskell
forward :: Foo a -> Bar a
forward (Foo x) (Left y) = Bar1 x y
forward (Foo x) (Right y) = Bar2 x y

backward :: Bar a -> Foo a
backward (Bar1 x y) = Foo x (Left y)
backward (Bar2 x y) = Foo x (Right y)
```

But we can also represent `Foo` as the polynomial $a(a + a)$ and `Bar` as the polynomial $2a^2$, but $a(a+a) = 2a^2$ so these types are isomorphic.
