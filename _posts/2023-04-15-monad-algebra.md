---
published: true
title: Monads are all about algebra
---

Ultimately, programs are all about turning data into action. But different languages approach this problem in different ways. Languages which favor a procedural style emphasize the action more than the data, so much so that they represent transformations of data in terms of an action! For example, `for` loops mutate an iteration value that never gets used again outside the loop, even though we often use `for` loops to transform data.

On the other hand, languages which favor a declarative style - like Haskell - emphasize the data over the action. We are way more concerned in forming the correct **representation** of our data, so that when we perform actions based on our data, we can express those actions easily.

The difference between a procedural and a declarative approach extends past programming - it applies to all sorts of problem solving in general. Consider this type of problem you might have seen in high school:

> Alice was 2 times as old as Bob 4 years ago. Alice is now 30 years old. How old is Bob now?

One way to solve it is by trial and error: we consider many possible solutions for Bob's age until we find the solution. Here, we are taking this problem at face value, and go straight to the computations. But on the other hand, we can *represent* the problem in a way that allows us to easily solve it. We can just do simple algebra - let $a$ represent Alice's current age and $b$ represents Bob's current age:

$$
\begin{align}
2(b - 4) &= a - 4 \\
2b - 8 &= a - 4 \\
b &= (a + 4) / 2 \\
  &= (30 + 4) / 2 \\
  &= 34 / 2 \\
  &= 17 \\
\end{align}
$$

And best of all, with algebra, we can come up with a schema for solving every problem similar to this! If $m$ represents how many times older Alice was $y$ years ago, then our solution is just

$$
b = (a - y + my)/m
$$

In the past, before algebra was developed, we could only use trial and error or convoluted ad hoc algorithms to solve arithmetic problems involving unknown values. But by providing a way of representing these problems in a way that is easy to work with, algebra allowed us to conquer all of these problems. With the development of abstract algebra, we generalized the simple algebra of numbers to new contexts, and one of these contexts just so happens to be computer programming!

Functional programming has been the new hype currently, but let's be honest: most people are still perfectly content with using the mainstream languages. I don't have a strong opinion either way. But I argue functional programming is less than a revolution than a natural evolution. Even if languages like Haskell remain niche, many of the ideas that Haskell expounded have become widespread. The idea of representing the problem correctly before solving it - that is the real revolution.

This post is yet another monad tutorial - well, sort of - but instead of making burrito analogies, we will go over the original mathematical context that inspired monads: as templates for *algebraic theories*. If you actually need to learn from scratch what monads are, I would suggest trying to digesting the burrito analogies instead. But if you know at least a bit, enjoy the ride!

## What is algebra?
We've probably first encountered algebra in high school, where it was about forming and manipulating expressions that represent arithmetic on numbers. And at this point, as many students would certainly have attested, we couldn't really see that much of a use yet. Due to a mix of teenage angst and bad pedagogy, we might have been unconvinced by the contrived examples in the algebra textbook - when was the last time you had to calculate someone's age other than from their birth date?

We can certainly sing the praises of algebra after we encounter arithemtic problems that are painful to solve without representing them correctly. Physics problems are a great example, since much of physics can be represented as relations between different quantities that can be expressed using equations. For many physics problems, we have little choice but to represent it algebraically first before solving it. Eventually, we might have so much experience with a certain type of problem that we could plug numbers into a calculator, but to be able to solve new problems, we must know some algebra.

But numbers are not the only thing that algebra is useful for. Ultimately, a number represents a type of **data**, and that type is precisely the quantity of something. Even within the world of numbers, we have different types of numbers: natural numbers, integers, rational numbers, real numbers, complex numbers, as well as some more elaborate constructions. All of these types of numbers follow the usual laws, like the distributive law:

```
a * (b + c) == a * b + a * c
```

but on the other hand, there are some things we cannot represent with some of these types. For example, we cannot represent arbitrary division, even by nonzero divisors, with natural numbers or integers. And we cannot represent square roots of negative numbers without the machinery of complex numbers.

Yet, other types of data have their own algebraic theories. You probably know a bit of **boolean algebra**, where our values can only be `True` and `False`, and our operations are `&&`, `||`, and `not`. There are laws in boolean algebra that have little parallel in the context of numbers, like de Morgan's law:

```haskell
not (a || b) == not a && not b

not (a && b) == not a || not b
```

Essentially, an **algebra** gives us a way to:

- to represent some sort of data,
- to represent operations on that data, and
- to manipulate those representations in a way that allows us to solve problems involving the data.

We will be a bit fast and loose with our terminology here, lest I get cruicified on the wall of the nLab data center (still, I love you nLab!). But it should be no surprise that algebras are central to Haskell research and development, as declarative programming is all about representing the data we want to work with in the nicest way. Often, after a bit of wrangling, we can represent the data the program will work with in some algebraic theory. Then, we can use the laws of those theories to help us simplify and operate on that data.

We are going to build our way up to monads here, but instead of getting into the burritos right away, I will go over a couple examples of algebras that are often used in Haskell, then we will see what the common patterns are - and how `Monad` (as well as `Functor`) provides *the* abstraction for those algebras.

## `Maybe`: to be, or not to be?
Consider the `Maybe` type:

```haskell
data Maybe a = Just a | Nothing
```

It is clear what `Maybe` means: it represents values that might or might not exist. These values can be of any type `a`, but when we talk about a `Maybe a` value, we are saying that we might not get an `a` out of it.

With a bit more thought, we can develop a nice little algebra for `Maybe` that allows us to easily work with it. First, how do we build `Maybe` terms representing the data of a value that might not exist? The constructor tells us exactly how to build expressions with type `Maybe a`: we can either construct `Just v` given a value `v` of type `a`, or `Nothing`. But notice that even if the value `v` is itself a `Maybe a` value, we can still form a `Maybe` expression, although this time it has the type `Maybe (Maybe a)`. This is similar to a situation in elementary algebra - we can add three or more values despite addition being a binary operation:

```haskell
a + b + c -- this expression
(a + b) + c -- could mean this
a + (b + c) -- or this.
```

and it works out since we have the **associative law**, so we don't have to write the brackets and our addition is well-defined. In the end, we can seamlessly combine this expression into a single value:

```haskell
a + b + c == (a + b) + c == a + (b + c)

3 + 4 + 5 
== (3 + 4) + 5 
== 3 + (4 + 5) 
== 12
```

Usually, in an algebra, we can not only construct expressions involving mere values, we can also construct expressions that in turn operate on other expressions. In that case, we would really like a rule to reduce these nested expressions as much as possible. `Maybe` is no different: if we have a value with type `Maybe (Maybe a)`, we want a way to simplify it back to a `Maybe a` value. Otherwise, we would probably end up with an unfathomably long chain of `Maybe`s before long.

These are all reasonable laws to expect:

```haskell
Nothing == Nothing
Just Nothing == Nothing
Just (Just v) == Just v 
```

We can write a function to implement this algebra:

```haskell
simplifyMaybe :: Maybe (Maybe a) -> Maybe a
simplifyMaybe Nothing = Nothing
simplifyMaybe (Just Nothing) = Nothing
simplifyMaybe (Just (Just v)) = Just v
```

There is still a potential problem though: say we have a `Maybe (Maybe (Maybe a))` value. It is possible that the value is `Just (Just (Just w))` for some value `w`, which means there could be two ways to initially apply the `Just (Just v) == Just v` law: either to the first pair of `Just`s first so that `v == Just w`, or the second pair so that `v == w`. But it turns out, it doesn't matter:

```haskell
-- Case #1: v == Just w 
Just (Just (Just w)) == Just (Just v) == Just v == Just (Just w)

-- Case #2: v == w
Just (Just (Just w)) = Just (Just (Just v)) == Just (Just v) == Just (Just w)
```

As it doesn't matter the order in which we apply the law, we can take as long of a chain of `Just`s as we can fit in a single value and always simplify it to a single `Just` in the end. And it doesn't matter that `simplifyMaybe` only takes a singly nested expression: if `Maybe` is nested `n` times, then we can simply apply `simplifyMaybe` `n` times.

Finally, we can freely make any value into a `Maybe` value:

```haskell
unitMaybe :: a -> Maybe a
unitMaybe = Just
```

When would this algebra be useful? Often, we use `Maybe` to represent the result of a function that is undefined on some values. For example, instead of returning `NaN` when the input is negative for `Float` values, we could define a square root function without that funny business:

```haskell
sqrtMaybe :: Float -> Maybe Float
sqrtMaybe x = if x >= 0 then Just $ sqrt x else Nothing
```

Of course, complex numbers are a thing, but we'll pretend they're imaginary here. Let's say we want to compute an `n`th root (for `n >= 2`, and we'll ignore the other cases for this example), not just a square root. When `n` is an integer, we can do so recursively by just iterating `n-1` times the `sqrtMaybe` function. But to do so, we would need to use `fmap` so we could pass the result of `sqrtMaybe`, which is `Maybe Float`, back into `sqrtMaybe`.

The fact that `Maybe` is a `Functor` really means that we can lift functions `a -> b` between plain old values to functions `Maybe a -> Maybe b` between **expressions** of the `Maybe` algebra. This is similar to how we can perform arithmetic operations on expressions, not just numbers:

```haskell
(3 + 4) * (5 + 6)
```

yet we can still simplify and evaluate them.

The function `fmap sqrtMaybe` has type `Maybe Float -> Maybe (Maybe Float)`. Given a `Maybe` expression, it yields a nested `Maybe` expression. This is a problem if we want to iterate it, since calculating the `n`th root this way would yield an `n-1` times nested expression. For example:

```haskell
-- 4th root of 16
sqrtMaybe <$> sqrtMaybe <$> sqrtMaybe 16
== sqrtMaybe <$> sqrtMaybe <$> Just 4
== sqrtMaybe <$> Just (Just 4)
== Just (Just (Just 2))
```

But now the solution is obvious: we've already come up with a scheme to simplify these nested expressions! Simply apply `simplifyMaybe` twice and we get:

```haskell
simplifyMaybe $ simplifyMaybe $ Just (Just (Just 2))
== simplifyMaybe $ Just (Just 2)
== Just 2
```

When implementing an `nthRoot` function recursively, we can just use `simplifyMaybe` each time:

```haskell
nthRoot :: Int -> Float -> Maybe Float
nthRoot n k
  | n < 2 = Nothing -- nth roots for n < 2 exist, but we'll ignore this case for now.
  | n > 2 = simplifyMaybe $ nthRoot (pred n) <$> sqrtMaybe k
  | otherwise = sqrtMaybe k
```

### Takeaways
In the `Maybe` algebra, we have
- a way of constructing a `Maybe` expression using the data constructors `Just` and `Nothing`,
- an operation `Just` on existing `Maybe` expressions, allowing us to chain multiple judgements that the value being referred to exists, and
- a way of collapsing these judgements into a single judgement using `simplifyMaybe`: the value simply exists and has this value, or it doesn't exist - not "this value exists $n$ times".

The `Maybe` constructor satisfies something similar to the associative law, which means there is only one way to simplify long chains of `Just`s despite `simplifyMaybe` being a binary operation.

More generally, in Haskell, an algebra gives us a way of constructing expressions over a base type `a` (`Just :: a -> Maybe a, Nothing :: Maybe a`), and a way of simplifying those expressions in a meaningful way (`simplifyMaybe`). A functor lifts pure functions `a -> b` into functions between expressions of our algebra (`Maybe a -> Maybe b`).

## `Reader`: move first, think later
Often, our computations involve unknown values. For example, at the start, we mentioned the general solution for problems taking the form

> Alice was $m$ times as old as Bob $y$ years ago. Alice is now $a$ years old. How old is Bob ($b$) now?

was $b = (a - y + my)/m$. We will focus on the special case where a value is unknown because it *depends* on some other value. And of course, the right way to represent those values is through a function `r -> a`. In the age example, if we represent the ages $(m, y, a)$ and $b$ as `Float`s, we have a function

```haskell
b :: (Float, Float, Float) -> Float
b (m, y, a) = (a - y + m * y) / m
```

We can represent these unknown values as a type

```haskell
newtype Reader r a = Reader {getReader :: r -> a}
```

and we want to develop an algebraic theory allowing us to more easily work with `Reader` expressions.

### Interlude: Functions are (sometimes) values
I used the term "value" loosely, even referring to functions `r -> a` as values. This is deliberate, as sometimes, functions should be treated as mere values! For example, a function $f: \{\bullet\} \to A$ from a one-element set is just a value of $A$. We can apply a function $g: A \to B$ to that value by just composing the functions: $gf: \{\bullet\} \to B$.

In this case, `r` might not be the `()` type (the closest analog to the one-element set in Haskell), so how are functions `f :: r -> a` still "values"? Well, for any function `g :: a -> b`, there is an obvious way to lift it to a function `(r -> a) -> (r -> b)`:

```haskell
lifted :: (r -> a) -> (r -> b)
lifted f = g . f
```

Once we know what the eventual value `v :: r` is, we can pass `v` to yield a value `f v :: a`, and apply `g` to it easily to get a value `g $ f v :: b`. So the one useful thing we can do with values of type `a` - applying functions to it - we can also do with values of type `r -> a` in an obvious way. The only difference between a value of type `r -> a` and a value of type `a` is that the first depends on some unknown value of type `r`. And as long as we know that there is only one eventual possibility for the unknown value, we can reason with a value of type `r -> a` just like we can with a value of type `a`. Long live handwaving!

### Continuing...
Let's go through the checklist again: to define an algebra, we have to define a way of constructing expressions, a way of operating on those expressions, and a way of simplifying these expressions in a meaningful way. We first fix a choice of type `r`, as it only makes sense when we work with a single choice of `r`. The first part is done: to construct a `Reader` expression, we have to provide a function `r -> a`. 

## Bonus: `State`
The `State` monad has, as expressions, functions `s -> (s, a)` for a state type `s` and an output type `a`. They represent functions that take in a state of type `s`, and return an updated state as well as an output of type `a`. The `State` monad explicitly models a procedural program.

```haskell
newtype State s a = State {runState :: s -> (s, a)}
```

Remembering that every monad is basically an algebraic theory, let's see what the theory is for the `State`. In our `State` algebra, we construct an expression by providing a *function* that updates a state and yields a value. Because our expressions can only be functions, the only operations are those that compose them in some way or another.
