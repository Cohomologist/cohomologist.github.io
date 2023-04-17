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

## Data structures as a free algebra
Let $U$ denote a set of of isomorphism classes of proper types with the singleton type $1 \in U$. We'll be fast and loose with our terminology here: even though there are more rigorous ways of talking about types, since this is a blog post, we'll handwave a bit here (sorry!). We can form the free $\mathbb{Z}$-module $M = \mathbb{Z}[U]$ as linear combinations of types with integer coefficients. Finally, we can define multiplication as simply joining words $u, w$ together: $u \cdot w = uw$.

Finally, we turn $M$ into a free commutative unital associative algebra $A$ as the quotient

$$
A = M/\langle uw = wu, 1w = w1 = w \mid u, w \in M \rangle
$$

And there we have it: now we have a way of reasoning about algebraic data structures. A sum $a + b$ represents a sum type `Either a b`, and a product represents a product type `(a, b)`. But how do we represent types that are constructed from other types? We can just quotient $A$ by relations representing those constructors. For example, if $m_a = 1 + a$ represents the `Maybe a` type, we just quotient $A$ by an ideal which forces the equality to hold:

$$
A/\langle m_a - 1 - a \rangle
$$

For all nonprimitive types in $U$, we want to impose the relations $R$ to yield a quotient algebra $A/R$ that allow us to reduce them to a sum and product of primitive types. In other words, if $P \subseteq A$ is the subalgebra consisting only of sums and products of primitive types, every equivalence class in $A/R$ should contain an element of $P$. From now on, whenever we use this algebra, if we don't mention a specific choice of $U$, assume that we let $U$ be the primitive types along with any nonprimitive types we use which we will provide suitable relations for.

## Derivatives of data structures
Of course, we have addition and multiplication, but we also take derivatives of data structures as well! This concept was introduced in the paper

> Abbott et al. (2005). [∂ for data: differentiating data structures](http://strictlypositive.org/dfordata.pdf). Fudamenta Informaticae.

but we will use different language to describe it. A *derivation* is an operator $D: A \to A$ that satisfies the Leibniz rule:

$$
D(pq) = D(p)q + pD(q)
$$

and we can define the derivative in this case to be the derivation $D(a) = 1$ for all $a \in U$. We can uniquely extend this to the whole algebra using the Leibniz rule and the linearity of $D$. The derivative $D$ acts just like you would expect: $D(a^n) = na^{n-1}$ for $a \in U$. 

Okay, cool, but what does the derivative actually mean? Well, consider the `Either a b` type for types `a, b` in $U$:

```haskell
data Pair a b = Pair a b
```

Let $p_{ab}$ denote this type. Then imposing the corresponding relation $p_{ab} = ab$, we can take the derivative to get

$$
D(p_{ab}) = D(ab) = D(a)b + aD(b) = a + b
$$

Essentially, the derivative punctures a *hole* into a data structure. When we omit a single part of a `Pair`, either the `a` or the `b`, we either have a `Pair a _` or a `Pair _ b` remaining. When we construct a product type $a_1 \dots a_n$, we have to give a value for each type $a_i$. But when we omit an arbitrary term $a_i$ so we only have to give $a_1 \dots a_{i-1} a_{i+1} \dots a_n$, we have to choose which one of $n$ values to omit, then choose the remaining $n-1$ terms. When $a_i = a$ for all $i$, that just reduces to the usual power rule $D(a^n) = na^{n-1}$! The Leibniz rule states for types $a, b \in U$ that $D(ab) = a + b$, which means that omitting an $a$ leaves us with a $b$ value, and omitting an $b$ leaves us with an $a$ value.

## Differential forms involving data structures

One advantage of doing algebra in a category of modules is the vast number of constructions we can use. Even if I were to be a bit less handwavy here, I would still try to carry over as much of the theory as possible to the context of modules.

A differential form is usually associated with a smooth manifold, but we can generalize it as the [universal differential envelope](https://ncatlab.org/nlab/show/universal+differential+envelope) of our derivative $D$. Let $\mu: A \otimes A \to A$ be the multiplication map, then we have a cochain complex of abelian groups

$$
0 \to \Omega^0(A) \xrightarrow{d} \Omega^1(A) \xrightarrow{d} \dots
$$

where $\Omega^0(A) = A, \Omega^1(A) = \ker(\mu), \Omega^k(A) = \Omega^1(A)^{k}$, and

$$
dp = 1 \otimes p - p \otimes 1
$$

This is universal in that there is a unique group homomorphism $\varphi : \Omega^1(A) \to A$ such that $\varphi d = D$.
