---
published: true
title: Data structures and algebra
---

I think one of the most magical things in all of computer science is that we can reason with data structures algebraically. Many data structures can be described as a sum and product of types:

```haskell
data Maybe a = Just a | Nothing
data List a = Nil | Cons a (List a)
```

Why are they called sums and products? A big reason is that the usual laws that we expect from sums and products hold for data structures as well, even if only up to isomorphism. Let $0$ denote the `Void` type, $1$ denote a singleton type `()`, and we will assume that all type variables have already been instantiated so that each term is a proper type which has kind `*`. Then, for example, the types

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

## The ring of data structures
Let $U$ denote a set of of isomorphism classes of proper types with the singleton type $1 \in U$. We'll be fast and loose with our terminology here: even though there are more rigorous ways of talking about types, since this is a blog post, we'll handwave a bit here (sorry, this post is more of a random amalgamation of thoughts than a serious theory.)

We can form a ring $A = \mathbb{Z}[[U]]$ of power series with integer coefficients and terms being mononials in $U$. For example, the type represented by the series $1 + a + a^2 + \dots$ is simply the list type `List a`.

And there we have it: now we have a way of reasoning about algebraic data structures. A sum $a + b$ represents a sum type `Either a b`, and a product $ab$ represents a product type `(a, b)`. Inductive types can be represented as infinite series, and we can easily embed $A$ into $\mathbb{C}[[U]]$ when we need analytic tools to work with our types.

## Derivatives of data structures
Of course, we have addition and multiplication, but we also take derivatives of data structures as well! This concept was introduced in the paper

> Abbott et al. (2005). [∂ for data: differentiating data structures](http://strictlypositive.org/dfordata.pdf). Fudamenta Informaticae.

but we will use different language to describe it. A *derivation* is an operator $D: A \to A$ that satisfies the Leibniz rule:

$$
D(pq) = D(p)q + pD(q)
$$

We can define the usual derivation as

$$
D\left(\sum_{i} a_i U_i\right) = \sum_i a_i D(U_i)
$$

where $D(U_i)$ is defined as $D(a) = 1$ for each type $a$ in the monomial $U_i$, and extended by the Leibniz rule. Okay, cool, but what does the derivative actually mean? Well, consider the ordered pair type `Pair a b` type for types `a, b` in $U$:

```haskell
data Pair a b = Pair a b
```

Let $p_{ab}$ denote this type. Then imposing the corresponding relation $p_{ab} = ab$, we can take the derivative to get

$$
D(p_{ab}) = D(ab) = D(a)b + aD(b) = a + b
$$

Essentially, the derivative punctures a *hole* into a data structure. When we omit a single part of a `Pair`, either the `a` or the `b`, we either have a `Pair a _` or a `Pair _ b` remaining. When we construct a product type $a_1 \dots a_n$, we have to give a value for each type $a_i$. But when we omit an arbitrary term $a_i$ so we only have to give $a_1 \dots a_{i-1} a_{i+1} \dots a_n$, we have to choose which one of $n$ values to omit, then choose the remaining $n-1$ terms. When $a_i = a$ for all $i$, that just reduces to the usual power rule $D(a^n) = na^{n-1}$! The Leibniz rule states for types $a, b \in U$ that $D(ab) = a + b$, which means that omitting an $a$ leaves us with a $b$ value, and omitting an $b$ leaves us with an $a$ value.

## Inductive types

### As initial algebras
In Haskell, unfortunately we can't describe infinite sums which is one way to describe data structures like lists and trees. But we can model inductive types with possibly recursive constructors as initial algebras of an endofunctor $F$. For example, the type `List a` with constructor

```haskell
data List a = Nil | Cons a (List a)
```

is the carrier for an initial algebra of the endofunctor

```haskell
data F b = Nil' | Cons' a b

fmap f Nil' = Nil'
fmap f (Cons' a b) = Cons' a (f b)
```

To model these types in our algebra, we can pretend that infinite sums exist and use [Adámek's fixed point theorem](https://ncatlab.org/nlab/show/Ad%C3%A1mek%27s+fixed+point+theorem) to compute the power series corresponding to a list. Given our initial object $0$, the carrier of an initial algebra for an endofunctor $F$ is simply the inductive limit

$$
\varinjlim_{n \in \mathbb{N}} F^n(0) = \mathop{\rm colim}(0 \to F(0) \to F^2(0) \to F^3(0) \to \dots)
$$

Computing the first few functor powers, knowing that it is impossible to construct a value of type `Void` (our intiial object),

```haskell
F Void == Nil' | Cons' a Void == Nil'
F (F Void) == Nil' | Cons' a (Nil' | Cons' a Void) == Nil' | Cons' a Nil'
F (F (F Void)) == Nil' | Cons' a (Nil' | Cons' a (Nil' | Cons' a Void)) == Nil' | Cons' a Nil' | Cons' a (Cons' a Nil')
```

We can also compute the functions `fmap absurd, fmap (fmap absurd)` where `absurd :: Void -> F Void` is the initial function:

```haskell
fmap absurd Nil' == Nil'

(fmap (fmap absurd)) Nil' == Nil'
(fmap (fmap absurd)) (Cons' a Nil') == Cons' a Nil'
```

So we can see that $F^n$ simply adjoins a list of length $n$ to the type of lists of length $\leq n - 1$, and the initial function gets lifted to the corresponding inclusion. That means that a list of length $n$ is itself an equivalence class across all $F^n(0)$ containing it, so the series is simply $1 + a + a^2 + \dots$.

### As fixed points

Let's go back to the definition of a list again:

```haskell
data List a = Nil | Cons a (List a)
```

Let $\ell = 1 + a + a^2 + \dots$ be the series for our list. From the constructor above, it should satisfy $\ell = 1 + a\ell$, and indeed it does:

$$
\ell = \sum_{n=0}^{\infty} a^n = 1 + \sum_{n=1}^{\infty} a^n = 1 + a\sum_{n=0}^{\infty} a^n = 1 + a\ell
$$

So $\ell$ is a fixed point for the map $p \mapsto 1 + ap$. Is $\ell$ the only fixed point? Consider the cyclic subgroup of $\mathrm{GL}_3(\mathbb{Z})$ generated by the matrices

$$
\begin{pmatrix}
1 & 0 & 0 \\
0 & 1 & 1 \\
0 & 0 & 1 \\
\end{pmatrix}
$$

act on $A$ as follows:

$$
\begin{pmatrix}
n & 0 & 0 \\
0 & 1 & k \\
0 & 0 & 1 \\
\end{pmatrix} \cdot p = a p + a^{k} + \dots + a + 1
$$


## Differential forms involving data structures

One advantage of doing algebra in a category of modules (rings are $\mathbb{Z}$-modules) is the vast number of constructions we can use. Even if I were to be a bit less handwavy here, I'd still try to carry over as much of the theory as possible to the context of modules because they're just so damned good!

A differential form is usually associated with a smooth manifold, but we can generalize it using Kähler differentials. Given our ring $A$, we can form the Kähler differentials $\Omega^1(A)$ as the universal $A$-module where every derivation $A \to M$ factors uniquely through the universal map $d: A \to \Omega^1(A)$. One way to construct $\Omega^1(A)$ is as the free $A$-module over the images $dp$ for all $p \in A$, modulo the relations

$$
d1 = 0, d(pq) = q \cdot dp + p \cdot dq, d(p+q) = dp + dq
$$

Differential forms allow us to express the space of all derivations simply as the space of $A$-linear maps

$$
\mathop{\rm Der}(A, -) \cong \mathrm{Hom}_{A\mathrm{Mod}}(\Omega^1(A), -)
$$

where given a derivation $D: A \to M$, we associate the linear map $D': \Omega^1(A) \to M$ defined on the differentials as $D'(dp) = D(p)$.

by essentially requiring the Leibniz rule to hold.

### Cohomology on $A$

We can form the de Rham complex

$$
0 \to \Omega^0(A) \xrightarrow{d} \Omega^1(A) \xrightarrow{d} \Omega^2(A) \xrightarrow{d} \dots
$$

where $\Omega^0(A) = A$ and $\Omega^k(A) = \Omega^1(A)^{\wedge k}$ for $k \geq 2$, from which we can get the de Rham cohomology

$$
0 \to H^0(A) \to H^1(A) \to H^2(A) \to \dots
$$
