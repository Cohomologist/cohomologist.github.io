---
published: true
title: Data structures and algebra
---

I think one of the most magical things in all of computer science is that we can reason with data structures algebraically. Many data structures can be described as a sum and product of types:

```haskell
data Maybe a = Just a | Nothing
data List a = Nil | Cons a (List a)
```

Why are they called sums and products? A major reason is that the usual laws that we expect from sums and products hold for data structures as well, even if only up to isomorphism. Let $0$ denote the `Void` type, $1$ denote a singleton type `()`, and we will assume that all type variables have already been instantiated so that each term is a proper type which has kind `*`. Then, for example, the types

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

But we can also represent `Foo` as the polynomial $a(a + a)$ and `Bar` as the polynomial $2a^2$, but $a(a+a) = 2a^2$. And as we will see, there is no coincidence that these types are isomorphic.

## Data structures as a free $\mathbb{Z}$-algebra
Let $U$ denote a set of of isomorphism classes of proper types with the singleton type $1 \in U$. We'll be fast and loose with our terminology here: even though there are more rigorous ways of talking about types, since this is a blog post, we'll handwave a bit here. We can form the free $\mathbb{Z}$-module $M = \mathbb{Z}[U]$ as linear combinations of types with integer coefficients. Finally, we can define multiplication as a bilinear map that simply joins words $u, w$ together: $u \cdot w = uw$.

Finally, we turn $M$ into a commutative unital algebra $A$ as the quotient

$$
A = M/\langle uw = wu, 1w = w1 = w \mid u, w \in M \rangle
$$

And there we have it: now we have a way of reasoning about algebraic data structures. A sum $a + b$ represents a sum type `Either a b`, and a product represents a product type `(a, b)`. But how do we represent types that are constructed from other types? We can just quotient $A$ by relations representing those constructors. For example, if $m_a = 1 + a$ represents the `Maybe a` type, we just quotient $A$ by an ideal which forces the equality to hold:

$$
A/\langle m_a - 1 - a \rangle
$$

For all nonprimitive types in $U$, we should impose the relations $R$ to yield a quotient algebra $A/R$ that allow us to reduce them to a sum and product of primitive types. In other words, if $P \subset A$ is the subalgebra consisting only of sums and products of primitive types, every equivalence class in $A/R$ should contain an element of $P$.
