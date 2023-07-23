---
title: Understanding exact sequences (part 1)
published: false
---
<p style="display: none">
$\newcommand{\oto}[1]{\overset{#1}{\to}}\newcommand{\im}{\mathrm{im}}\newcommand{\coker}{\mathrm{coker}}\newcommand{\Hom}{\mathrm{Hom}}$
</p>**Exact sequences** provide a neat way to express relations between algebraic objects. An exact sequence is a chain of objects

$$
A^0 \oto{d^1} A^1 \oto{d^2} \dots \oto{d^n} A^n
$$

where the image of a map is annihilated by the next:

$$
\im(d^i) = \ker(d^{i+1})
$$

Exact sequences are a neat tool to have, since many relations between algebraic objects can be expressed in terms of kernels and images. For example, a map $d: A \to B$ is injective if and only if $\ker(d) = 0$, and surjective if and only if $\im(d) = B$.

> Exact sequences are defined in any **abelian category**, which encompasses all algebraic theories which behave like abelian groups. Those include abelian groups (duh!), modules, vector spaces, and sheaves valued in those structures.

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
\color{#7a8cee}{A^0 \oto{d^1} A^1 \oto{d^2} \dots \oto{d^m} A^m \oto{\delta^A}~}&\color{#c478dc}{I^0 \oto{e^1} I^1 \oto{e^2} \dots \oto{e^q} I^q} \\
&\color{#c478dc}{I^0 \oto{e^1} I^1 \oto{e^2} \dots \oto{e^q} I^q} \color{#f38a89}{\oto{\delta^B} B^0 \oto{f^1} B^1 \oto{f^2} \dots \oto{f^n} B^n}
\end{align}
$$

that overlap in the middle. Can we join them together?

$$
\color{#7a8cee}{A^0 \oto{d^1} A^1 \oto{d^2} \dots \oto{d^m} A^m \oto{\delta^A}}\color{#c478dc}{I^0 \oto{e^1} I^1 \oto{e^2} \dots \oto{e^q} I^q} \color{#f38a89}{\oto{\delta^B} B^0 \oto{f^1} B^1 \oto{f^2} \dots \oto{f^n} B^n}
$$

If the middle sequence $\color{#c478dc}{I^0 \oto{e^1} \dots \oto{e^q} I^q}$ has more than two objects (so that $q \geq 1$), we can always join the sequences together. Otherwise, if the middle sequence only consists of one object – so it isn't much of a sequence of all – we require $\im(\delta^A) = \ker(\delta^B)$.

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
> For example, we can build a complex number as a real polynomial in a single variable $i$, which lives in the polynomial ring $\mathbb{R}[i]$. By itself, the variable $i$ is just a placeholder. But complex numbers manifest a simple yet genius idea: simply pretend that $i^2 = -1$, giving us a *free* square root $i = \sqrt{-1}$. All the usual arithmetic of complex numbers comes from using both the arithmetic of polynomials, and the additional equation $i^2 = -1$ - for example: $$\begin{align}
(a+bi)(c+di) &= ac + adi + bci + bd\color{#7a8cee}{i^2} \\
&= ac + adi + bci + bd\color{#7a8cee}{(-1)} \\
&= (ac - bd) + (ad + bc)i
\end{align}
$$
> 
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

Is there a similar idea for injective but not surjective maps (inclusions)? Yes! Suppose $d: A \to B$ is such a map. We have the opposite problem, where

$$0 \to A \oto{d} B$$

is exact but we cannot extend this sequence to the right with $B \to 0$ since $d$ is not surjective. However, we can map only $\im(d)$ onto $0$ by constructing the quotient $B/\im(d)$ and projecting $B$ onto it, giving us the exact sequence

$$
0 \to A \oto{d} B \color{#f38a89}{\oto{\pi} B/\im(d) \to 0}
$$

It is now the nonzero points of $B/\im(d)$ stop $d$ from achieving its dream of being surjective, which encompass the points to which $d$ does *not* map. Clearing this obstruction is more intuitive – and disappointing. To make $d$ surjective, we have to force its image to be all of $B$. By exactness, $\im(d) = \ker(\pi)$, so we can look at the kernel instead. To force the kernel of $\pi$ to be all of $B$, we have to force all nonzero points of $B/\im(d)$ to be zero, which we can do via the quotient $B/{(B/\im(d))}$. But it turns out, this quotient is simply $\im(d)$! In the end, we get the exact sequence

$$
0 \to A \oto{\tilde{d}} \im(d) \to 0
$$

where $\tilde{d}$ is the same map, just with a different target space. But this was all a bit obvious, since any function that targets its image is surjective by definition. There is still some insight here though – we could view $\im(d)$ as merely a subspace of $B$, but consider that we can obtain $\im(d)$ by constructing a space $B/\im(d)$ of points that are not in $\im(d)$, then simply forcing those points to be $0$ with the quotient $B/(B/\im(d))$ to excise them  from $B$. It makes things a bit more tangible, and explains why taking a quotient twice results in the original space.

We also have a new fancy term: we call $B/\im(d)$ the **cokernel** $\coker(d)$ of $d$, which represents points that $d$ does not map to – or in other words, the failure of $d$ to be surjective.

Finally, even if $d$ is both not injective and not surjective, we can still combine these two constructions to construct a relevant exact sequence. For every map $d: A \to B$, we have the exact sequence

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

--- 

### "Be isomorphic, damn it!"

Even today, solving equations is still a key problem in algebra. Consider differential equations – they're a weird idea to even start with at first, since after getting used to the $x$s and stuff being numbers, it's definitely a leap to solve for a function. But much of modern math is all about studying algebras of functions, as functions on a space give us information about the space itself.

Given a smooth manifold $M$, its space of smooth functions $\Omega^0(M)$ is a vector space. The differentials of those functions are contained in the space of $1$-forms $\Omega^1(M)$. Indeed, there is a map $d: \Omega^0(M) \to \Omega^1(M)$ sending a function $f$ to its differential $df$. 

Consider the differential equation $df = g$ where $f$ has to be a smooth function. Given a $1$-form $g$ (something that can be integrated along a curve), does the equation have a solution? First, we determine whether $dg \neq 0$. If the answer is yes, then as the differential of a differential must be $0$, we know that $df = g$ cannot have a solution. But if $dg = 0$, what do we do now?

#### Cochain complexes: almost heaven
Well, the differential operator $d$ has the nice property that the differential of a differential is $0$, so we can form the **cochain complex**

$$
0 \to \Omega^0(M) \oto{d} \Omega^1(M) \oto{d} \Omega^2(M) \oto{d} \dots
$$

Wait, what is a cochain complex? It is similar to an exact sequence:

$$
0 \to A^0 \oto{d^1} A^1 \oto{d^2} A^2 \oto{d^3} \dots
$$

only that cochain complexes relax the requirements for an exact sequence by only requiring that $\im(d^{i-1}) \subseteq \ker(d^{i})$. Cochain complexes are useful since we can obtain an exact sequence from their **cohomology**, which is a sequence of objects defined as

$$H^{i}(A^i) := \ker(d^{i})/\im(d^{i-1})$$

Under the induced quotient maps $\tilde{d^i}$, the cohomology sequence

$$
0 \to H^{i}(A^0) \oto{\tilde{d^1}} H^{i}(A^1) \oto{\tilde{d^2}} H^{i}(A^2) \oto{\tilde{d^3}} \dots 
$$

is exact.

The cohomology of the cochain complex

$$
\begin{align}
0 \to \Omega^0(M) \oto{d} &\Omega^1(M) \oto{d} \Omega^2(M) \oto{d} \dots \\
&\Downarrow \\
H^{0}(M) \oto{\tilde{d}} &H^{1}(M) \oto{\tilde{d}} H^{2}(M) \oto{\tilde{d}} \dots 
\end{align}
$$

is called **de Rham cohomology**. But even though the de Rham cohomology sequence is exact, why is it useful? 

#### Potholes in the space

Remember that we wanted to solve the equation $df = g$ for $f$. 

> When we refer to the differential $d$, we might mention the specific map $d^i: \Omega^{i-1}(M) \to \Omega^i(M)$ we're talking about to clear any confusion.

As we mentioned, we must have $dg = 0$ for the equation to even be solvable, so already we know that $g \in \ker(d^2)$. Whenever $df = g$ has a solution $f$, it means that $g \in \im(d^1)$, so $g$ must be projected to $0$ in the de Rham cohomology space $H^{1}(M) = \ker(d^2)/\im(d^1)$.

Luckily, de Rham's theorem states that de Rham cohomology $H^i(M)$ is isomorphic to the manifold's **singular cohomology** $H^i(M, \mathbb{R})$, which is a purely topological property not reliant on smooth functions at all! That means the reason we can't solve the differential equation is because of the manifold itself as a space, which is refreshing since so many of the obstructions in calculus come from the most silly things.

If $M$ is just good old Euclidean space $\mathbb{R}^n$, then $df = g$ is always solvable as $\mathbb{R}^n$ is contractible to a point. So the de Rham cohomology is the singular cohomology of $\mathbb{R}^n$, which is in turn the singular cohomology of a single point:

$$
H^i(\mathbb{R}^n) \cong H^i(\{0\}, \mathbb{R}) \cong \begin{cases}
\mathbb{R} &i=0 \\
0 &i>0
\end{cases}
$$

We have $H^1(\mathbb{R}^n) = \ker(d^2)/\im(d^1) = 0$, which means $\ker(d^2) = \im(d^1)$. That means simply knowing that $dg = 0$ is enough to conclude that the differential equation $df = g$ has a solution $f$.

--- 

But if we decide to puncture a hole into the manifold so we get $\mathbb{R}^n \setminus \{0\}$, matters become more hairy. We can compute the cohomology purely using topological arguments thanks to de Rham's theorem (for me, the less calculus, the better). In the case of $\mathbb{R} \setminus \{0\}$, we can cover the manifold with the intervals $(-\infty, 0), (0, \infty)$. Both intervals are homeomorphic to $\mathbb{R}$, which we already know the cohomology of. Then we can use its **Mayer-Vietoris sequence**

$$
\begin{align}
&0 \to H^0(\mathbb{R} \setminus \{0\}, \mathbb{R}) \to H^0(\mathbb{R}, \mathbb{R}) \oplus H^0(\mathbb{R}, \mathbb{R}) \to H^0(\varnothing, \mathbb{R}) \to H^1(\mathbb{R} \setminus \{0\}, \mathbb{R}) \to \dots \\
\cong ~&0 \to H^0(\mathbb{R} \setminus \{0\}, \mathbb{R}) \to \mathbb{R}^2 \to 0 \to H^1(\mathbb{R} \setminus \{0\}, \mathbb{R}) \to 0 \to 0 \to H^2(\mathbb{R} \setminus \{0\}, \mathbb{R}) \to \dots 
\end{align}
$$

which is exact. Already, we can identify two isomorphisms!

$$
\begin{align}
&\color{#7a8cee}{0 \to H^0(\mathbb{R} \setminus \{0\}, \mathbb{R}) \to \mathbb{R}^2 \to 0} \to H^1(\mathbb{R} \setminus \{0\}, \mathbb{R}) \to 0 \to 0 \to H^2(\mathbb{R} \setminus \{0\}, \mathbb{R}) \to \dots \\
&0 \to H^0(\mathbb{R} \setminus \{0\}, \mathbb{R}) \to \mathbb{R}^2 \to \color{#f38a89}{0 \to H^1(\mathbb{R} \setminus \{0\}, \mathbb{R}) \to 0 \to 0} \to H^2(\mathbb{R} \setminus \{0\}, \mathbb{R}) \to \dots 
\end{align}
$$

and now we have our answer: $H^0(\mathbb{R} \setminus \{0\}) \cong \mathbb{R}^2$ and $H^i(\mathbb{R} \setminus \{0\}) \cong 0$ for $i \geq 1$.

For $\mathbb{R}^n \setminus \{0\}$ where $n \geq 2$, we can retract the manifold onto the $n-1$-sphere $S^{n-1}$. Then to cover $S^{n-1}$, we can choose two hemispheres $A, B$ and stretch them towards each other so their intersection $A \cap B$ forms a strip that can be retracted onto a copy of $S^{n-2}$. The hemispheres $A, B$ are both contractible. Using the Mayer-Vietoris sequence again:

$$
\begin{align}
&0 \to H^0(\mathbb{R}^n \setminus \{0\}, \mathbb{R}) \to H^0(\{0\}, \mathbb{R}) \oplus H^0(\{0\}, \mathbb{R}) \to H^0(S^{n-2}, \mathbb{R}) \to H^1(\mathbb{R} \setminus \{0\}, \mathbb{R}) \to \dots \\
\cong ~&0 \to H^0(\mathbb{R}^n \setminus \{0\}, \mathbb{R}) \to \mathbb{R}^2 \to H^0(S^{n-2}, \mathbb{R}) \to H^1(\mathbb{R}^n \setminus \{0\}, \mathbb{R}) \to 0 \to H^0(S^{n-2}, \mathbb{R}) \to \dots 
\end{align}
$$

##### The $n=2$ case
When $n = 2$, the $0$-sphere $S^0$ is simply two disconnected points, onto which $\mathbb{R} \setminus \{0\}$ retracts. In this case,

$$
0 \to H^0(\mathbb{R}^2 \setminus \{0\}, \mathbb{R}) \to \mathbb{R}^2 \to \mathbb{R}^2 \to H^1(\mathbb{R}^2 \setminus \{0\}, \mathbb{R}) \to 0 \to 0 \to H^2(\mathbb{R}^2 \setminus \{0\}, \mathbb{R}) \to \dots
$$

To compute $H^0(\mathbb{R}^2 \setminus \{0\}, \mathbb{R}) \cong H^0(S^1, \mathbb{R})$ and $H^1(\mathbb{R}^2 \setminus \{0\}, \mathbb{R}) \cong H^1(S^1, \mathbb{R})$, we'll use something a bit overkill here due to sheer laziness. We can use the **universal coefficient theorem** exact sequence

$$
0 \to \mathrm{Ext}_{\mathbb{R}}^1(H_{i-1}(S^1, \mathbb{R}), \mathbb{R}) \to H^i(S^1, \mathbb{R}) \to \hom(H_i(S^1, \mathbb{R}), \mathbb{R}) \to 0
$$

Since $\mathbb{R}$ is a vector space, it is injective as a module, so $\mathrm{Ext}_{\mathbb{R}}^1(H_0(S^1, \mathbb{R}))$ vanishes and we just have the exact sequence

$$
0 \to H^i(S^1, \mathbb{R}) \to \hom(H_i(S^1, \mathbb{R}), \mathbb{R}) \to 0
$$

For $H^0(S^1, \mathbb{R})$, since $S^1$ is path-connected, we have $H_0(S^1, \mathbb{R}) \cong H_0(\{0\}, \mathbb{R}) = \mathbb{R}$, so we get the exact sequence

$$
0 \to H^i(S^1, \mathbb{R}) \to \mathbb{R} \to 0
$$

and can conclude $H^0(S^1, \mathbb{R}) \cong \mathbb{R}$. We can make a similar argument for $n \geq 2$ as well, so in general, $H^0(S^{n-1}, \mathbb{R}) = \mathbb{R}$.

Notice that the fundamental group $\pi^1(S^1) = \mathbb{Z}$ which is isomorphic to the homology $H_1(S^1, \mathbb{Z})$. We have $H_1(S^1, \mathbb{R}) \cong H_1(S^1, \mathbb{Z}) \otimes \mathbb{R} \cong \mathbb{R}$. We can also use the cohomological version to get the exact sequence

$$
0 \to \mathrm{Ext}_{\mathbb{R}}^1(H_0(S^1, \mathbb{R}), \mathbb{R}) \to H^1(S^1, \mathbb{R}) \to \hom(H_1(S^1, \mathbb{R}), \mathbb{R}) \to 0
$$

We have $\hom(H_1(S^1, \mathbb{R}), \mathbb{R}) \cong \hom(\mathbb{R}, \mathbb{R}) \cong \mathbb{R}$ as there are no nontrivial ring automorphisms $\mathbb{R} \to \mathbb{R}$, so we get the exact sequence

$$
0 \to H^1(S^1, \mathbb{R}) \to \mathbb{R} \to 0
$$

which means $H^1(S^1, \mathbb{R}) \cong \mathbb{R}$. The remaining cohomology objects are $0$ as we can see from the Mayer-Vietoris sequence.

##### In general

We can extend the result to $n \geq 3$ by induction. Already, we know that $H^0(\mathbb{R}^n \setminus \{0\}, \mathbb{R}) \cong H^0(S^{n-1}, \mathbb{R}) = \mathbb{R}$. Consider a small segment of the exact sequence for $i \geq 1$

$$
H^{i-1}(S^{n-1}, \mathbb{R}) \to \color{#7a8cee}{0 \to H^{i-1}(S^{n-2}, \mathbb{R}) \to H^{i}(S^{n-1}, \mathbb{R}) \to 0 \to \dots}
$$

We have the isomorphism $H^{i-1}(S^{n-2}, \mathbb{R}) \cong H^{i}(S^{n-1}, \mathbb{R})$. So if we've established that

$$
H^{i}(S^{n-2}, \mathbb{R}) \cong \begin{cases}
	\mathbb{R} &i \in \{0, n-2\} \\
	0 & i \not\in \{0, n-2\}
\end{cases}
$$

we can establish that 

$$
H^{i}(S^{n-1}, \mathbb{R}) \cong \begin{cases}
	\mathbb{R} &i \in \{0, n-1\} \\
	0 & i \not\in \{0, n-1\}
\end{cases}
$$

so extending by induction and applying de Rham's theorem, in the end we have the de Rham cohomology:

$$
H^{i}(\mathbb{R} \setminus \{0\}) \cong \begin{cases}
	\mathbb{R}^2 &i = 0 \\
	0 & i > 0
\end{cases}, H^{i}(\mathbb{R}^n \setminus \{0\}) \cong \begin{cases}
	\mathbb{R} &i \in \{0, n-1\} \\
	0 & i \not\in \{0, n\}
\end{cases}
$$

---

As an example of why this might be useful, the de Rham cohomology of punctured Euclidean space suggests a potential problem with solving $df = g$ in $\mathbb{R}^2$. As $H^{1}(\mathbb{R}^2 \setminus \{0\}) \cong \mathbb{R}$, even if $g$ is in the kernel of the differential map, the cohomology suggests that $g$ might not be the differential of a smooth function. For example, let

$$
g = \frac{x\,dy - y\,dx}{x^2 + y^2}
$$

Suppose there was a solution $f$ to $df = g$. We can use Stoke's theorem to integrate this form on the circle $S^1 \subseteq \mathbb{R}^2 \setminus \{0\}$, which is the boundary of the closed unit disc $D$:

$$
\int_{S^1} g = \int_{S^1} df = \int_{D} d(df) = \int_D 0 = 0
$$

But on the other hand, with a bit of substitution:

$$
\int_{S^1} g = \int_0^{2\pi} \frac{\sin(\theta)^2 + \cos(\theta)^2}{\sin(\theta)^2 + \cos(\theta)^2}~d\theta = \int_{0}^{2\pi} d\theta = 2\pi
$$

Since $g$ is a nonzero form in $H^{1}(\mathbb{R}^2 \setminus \{0\}) = \mathbb{R}$, every $1$-form $\omega$ on the punctured plane is contained within some coset $\im(d^1) + \lambda g$ for some scalar $\lambda \in \mathbb{R}$.

