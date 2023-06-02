---
title: Understanding exact sequences (part 1)
published: true
---
<p style="display: none">
$\newcommand{\oto}[1]{\overset{#1}{\to}}\newcommand{\im}{\mathrm{im}}\newcommand{\coker}{\mathrm{coker}}\newcommand{\Hom}{\mathrm{Hom}}$
</p>**Exact sequences** provide a neat way to express relations between algebraic objects. An exact sequence is a chain of objects

$$
A_0 \oto{d_1} A_1 \oto{d_2} \dots \oto{d_n} A_n
$$

where the image of a map is annihilated by the next:

$$
\im(d_i) = \ker(d_{i+1})
$$

Exact sequences are a neat tool to have, since many relations between algebraic objects can be expressed in terms of kernels and images. For example, a map $d: A \to B$ is injective if and only if $\ker(d) = 0$, and surjective if and only if $\im(d) = B$.

> We will work in an **abelian category**, which encompasses all algebraic theories that behave like abelian groups. These include abelian groups (duh!), modules, vector spaces, and sheaves valued in those structures. However, we will get a bit fast and loose with our reasoning here (sorry!).

### Basic relations
Let $d: A \to B$ be a map. 

- $d$ is **injective** if and only if $0 \to A \oto{d} B$ is exact.
- $d$ is **surjective** if and only if $A \oto{d} B \to 0$ is exact.
- $d$ is an **isomorphism** if and only if $0 \to A \oto{d} B \to 0$ is exact.

### Joining exact sequences
Notice that the exact sequence for isomorphisms seems to be built from the exact sequences for injections and surjections:

$$
\begin{align}
\color{#7a8cee}{0 \to A \oto{d} B} \to 0 \\
0 \to \color{#f38a89}{A \oto{d} B \to 0}
\end{align}$$

In general, say we have two exact sequences

$$
\begin{align}
\color{#7a8cee}{A_0 \oto{d_1} A_1 \oto{d_2} \dots \oto{d_m} A_m \oto{\delta_A}~}&\color{#c478dc}{I_0 \oto{e_1} I_1 \oto{e_2} \dots \oto{e_q} I_q} \\
&\color{#c478dc}{I_0 \oto{e_1} I_1 \oto{e_2} \dots \oto{e_q} I_q} \color{#f38a89}{\oto{\delta_B} B_0 \oto{f_1} B_1 \oto{f_2} \dots \oto{f_n} B_n}
\end{align}
$$

that overlap in the middle. Can we join them together?

$$
\color{#7a8cee}{A_0 \oto{d_1} A_1 \oto{d_2} \dots \oto{d_m} A_m \oto{\delta_A}}\color{#c478dc}{I_0 \oto{e_1} I_1 \oto{e_2} \dots \oto{e_q} I_q} \color{#f38a89}{\oto{\delta_B} B_0 \oto{f_1} B_1 \oto{f_2} \dots \oto{f_n} B_n}
$$

If the middle sequence $\color{#c478dc}{I_0 \oto{e_1} \dots \oto{e_q} I_q}$ has more than two objects (so that $q \geq 1$), we can always join the sequences together. Otherwise, if the middle sequence only consists of one object – so it isn't much of a sequence of all – we require $\im(\delta_A) = \ker(\delta_B)$.

The exact sequence for isomorphisms has two overlapping objects in the middle, so we can join those sequences together without verifying exactness.

### Breaking up a map into its parts

A map $d: A \to B$ might not be injective nor surjective, but does it mean we have to chuck our exact sequences down the trash?

Let's consider the first case first. Suppose that $d$ is surjective but not injective. There's a simple reason why $d$ is not injective: there are points $a, b \in A$ such that $d(a) = d(b)$.

But in algebra, we like to do the next best thing and *force* things to have the properties we want by imposing additional relations on the objects involved. In this case, we can simply force points that have equal images to be equal to each other:
$$
a \sim b \Leftrightarrow d(a) = d(b)
$$

In the end, we don't get $A$, but we do get the **quotient space** $A/{\ker(d)}$ – to say that $d(a) = d(b)$ is the same as saying $d(a - b) = 0$, or equivalently, that $a - b \in \ker(d)$.

> As an aside, I don't like how a lot of books on (abstract) algebra motivate quotient spaces. Algebra is all about reasoning with equations, and quotient spaces simply give us additional equations to play with! Instead, these books emphasize their construction too much, which is confusing until we understand their original purpose in the first place.
> 
> For example, we can build a complex number as a real polynomial in a single variable $i$, which lie in the polynomial ring $\mathbb{R}[i]$. By itself, the variable $i$ is just a placeholder. But complex numbers manifest a simple yet genius idea: simply pretend that $i^2 = -1$, giving us a *free* square root $i = \sqrt{-1}$. All the usual arithmetic of complex numbers comes from using both the arithmetic of polynomials, and the additional equation $i^2 = -1$ - for example: $$\begin{align}
(a+bi)(c+di) &= ac + adi + bci + bd\color{#7a8cee}{i^2} \\
&= ac + adi + bci + bd\color{#7a8cee}{(-1)} \\
&= (ac - bd) + (ad + bc)i
\end{align}
$$
> $\mathbb{C}$ is the quotient ring $\mathbb{R}[i]/(i^2 + 1)$, which is precisely the construction that imposes the relation $i^2 = -1$ on $\mathbb{R}[i]$.

In general, whenever we take a quotient $A/I$, we are essentially forcing the points of $I$ to be $0$. If we can express a point of $I$ as a difference $a - b$, then as a result, we have $a = b$ in $A/I$.

Every equivalence class has the same image under $d$, as $a - b \in \ker(d)$ is the same as $d(a) = d(b)$, so we can construct a map $\tilde{d}: A/{\ker(d)} \to B$ that simply maps each equivalence class to their common image. By forcing $\ker(d)$ to be $0$ via the quotient, $\tilde{d}$ is now injective in addition to being surjective, and we have an isomorphism.

Of course, this is just the first isomorphism theorem. But the idea is more important: the nonzero points in the kernel $\ker(d)$ obstruct $d$ from being injective. When we simply pretend those points are $0$, we clear the obstruction and the kingdom is saved.

We can also see the idea using exact sequences. As $d$ is surjective, we have the exact sequence

$$
A \oto{d} B \to 0
$$

$d$ is not injective, so we cannot extend the exact sequence to the left with $0 \to A$. But, we can always extend this exact sequence by including the kernel of $d$ into $A$:

$$
\color{#7a8cee}{\ker(d) \oto{i}} A \oto{d} B \to 0
$$

We have $\im(i) = \ker(d)$ so the sequence is indeed still exact. Since $i$ is injective, we have the exact sequence $0 \to \ker(d) \oto{i} A$, which we can join to get the exact sequence

$$
\color{#7a8cee}{0 \to \ker(d) \oto{i}} A \oto{d} B \to 0
$$

We can see that this sequence is *almost* $0 \to A \oto{i} B \to 0$ – the isomorphism we want – but there is a pesky nonzero $\ker(d)$ obstructing us. No fear though, for the quotient is here:

$$
0 \to A/{\ker(d)} \oto{\tilde{d}} B \to 0
$$

Note that since $i$ is injective, $\ker(d) = \im(i)$, so $A/{\ker(d)} = A/\im(i)$:

$$
0 \to A/{\im(i)} \oto{\tilde{d}} B \to 0
$$

Is there a similar idea for injective but not surjective maps (inclusions)? Yes! Suppose $d: A \to B$ is such a map. We have the opposite problem: $0 \to A \oto{d} B$ is exact but we cannot extend this sequence to the right with $B \to 0$ since $d$ is not surjective. However, we can map only $\im(d)$ onto $0$ by constructing the quotient $B/\im(d)$ and projecting $B$ onto it, giving us the exact sequence

$$
0 \to A \oto{d} B \oto{\pi} B/\im(d) \to 0
$$

It is now the nonzero points of $B/\im(d)$ stop $d$ from achieving its dream of being surjective, which encompass the points to which $d$ does *not* map. Clearing this obstruction is more intuitive – and disappointing. To make $d$ surjective, we have to force its image to be all of $B$. By exactness, $\im(d) = \ker(\pi)$, so we can look at the kernel instead. To force the kernel of $\pi$ to be all of $B$, we have to force all nonzero points of $B/\im(d)$ to be zero, which we can do via the quotient $B/{(B/\im(d))}$. But it turns out, this quotient is simply $\im(d)$! In the end, we get the exact sequence

$$
0 \to A \oto{\tilde{d}} \im(d) \to 0
$$

where $\tilde{d}$ is the same map, just with a different target space. But this was all a bit obvious, since any function that targets its image is surjective by definition. There is still some insight here though – we could view $\im(d)$ as merely a subspace of $B$, but consider that we can obtain $\im(d)$ by constructing a space $B/\im(d)$ of points that are not in $\im(d)$, then forcing those points to be $0$ with the quotient $B/(B/\im(d))$. It makes things a bit more tangible, and explains why taking a quotient twice results in the original space.

We also have a new fancy term: we call $B/\im(d)$ the **cokernel** $\coker(d)$ of $d$, which represents points that $d$ does not map to – or in other words, the failure of $d$ to be surjective.

--- 

To put it all together, for every map $d: A \to B$, we have the exact sequence

$$
0 \to \ker(d) \oto{i} A \oto{d} B \oto{\pi} \coker(d) \to 0
$$

The kernel $\ker(d)$ represents the failure for $d$ to be injective by allowing us to identify points that have a common image under $d$. The cokernel $\coker(d)$ represents the failure for $d$ to be surjective by representing the points that are not mapped to by $d$.

If $\ker(d) = 0$ and $\coker(d) = 0$, that's great: we have an isomorphism. But if either $\ker(d) \neq 0$ or $\coker(d) \neq 0$, we can take a quotient of $ A$ or $B$ respectively, adjust the map $d$ accordingly, then pretend that $d$ is an isomorphism $\tilde{d}$:

$$
\begin{align}
0 \to A/{\ker(d)} &\oto{\tilde{d}} B/{\coker(d)} \to 0 \\
= 0 \to A/{\ker(d)} &\oto{\tilde{d}} \im(d) \to 0
\end{align}
$$
