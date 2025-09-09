## 1 Isomorphisms

> **Definition**  
> A map $f: A \to B$ is called an **isomorphism** or invertible map , if there exists a map $g: B \to A$ that holds $f \circ g = 1_B$ and $g \circ f = 1_A$. We also say $A$ and $B$ are **isomorphic** if there is at least one isomorphism between them.

We also call the $g$ we mention above the inverse map of $f$, denoted by $f^{-1}$.

Isomorphism has *reflexivity*, *symmetry*, *transitivity*. 


### 1.1 General Division Problems
Actually, all maps hold *reflexivity*, *symmetry*, *transitivity* and also contain identity maps. This is very similar to multiplication without commutativity. For instance, let $f$ and $g$ be maps; $a$ and $b$ be real numbers:
$$ 
\begin{split}
\frac{1}{a}, \frac{1}{b} \sim& f^{-1}, g^{-1} \\
a \times b \sim& f \circ g \\
\end{split}
$$
Then we roughly generalize division to maps. Division between two real numbers are about multiplying one with the inverse number of the other. But we know that 0 can not be a dividend. This causes the following unexpected cases:
- $0 \times x = a, a \ne 0$ has no solution.
- $0 \times x = 0$ has infinite solutions.

The two cases are actually analogous to the *determination* and *choice* problems (we call them *general division problems*) about maps.

#### Determination Problem
Given two maps $f$ and $h$ as shown below. Analogous to $a \times x = b$, the problem is to figure out all $g$, if any, for which $f \circ g = h$. <center><img alt="cat" src="https://i.ibb.co/GvyDVHkd/determination-problem-drawio.png" width="24%" /></center>
For the most restricted case, B is a one-element set denoted by $\{b\}$. Then $$ h(x) = (g \circ f)(x) = g(f(x)) = g(b) $$
We can note that $x$ is eliminated in the result, which means whatever element of $A$ is mapped to one constant element of $C$. We can call $h$ *constant* because it always gives the same result no matter how $x$ varies.

#### Choice Problem
Given two maps $g$ and $h$ as shown below. Analogous to $x \times a = b$, the problem is to figure out all $f$. if any, for which $f \circ g = h$. <center><img alt="cat" src="https://i.ibb.co/xKg3RJCw/choice-problem-drawio-1.png" width="24%" /></center>
For the case when $A =C$, $h$ is actually an identity map denoted $1_A$ (or $1_C$). the map $f$, which we want to figure out, is now an inverted map of $g$ for which $f \circ g = 1_A$. Consider the following case:<center><img alt="cat" src="https://i.ibb.co/cS20Cx5C/choice-problem-2-drawio.png" width="55%" /></center>
When $g$ is defined as the figure on the left, it is surprised that we can find exactly two solutions $f_1$ and $f_2$. Conversely, if $f_1$ is given first and the problem is to find out all $g$ if any, it becomes a determination problem. Besides the two elements of $B$ that $f_1$ returns from ones in $A$, there is still one element left (in the middle) which can choose to be taken to the any of the two elements of $A$. Hence there are two such $g$:<center><img alt="cat" src="https://i.ibb.co/Pzxx8kGZ/choice-problem-3-drawio.png" width="45%" /></center>

We find they both have two solutions.

### 1.2 Retractions, Sections and Idempotents
In the previous chapter, we talk about determination and choice problems, while retractions and sections are just special cases where $h$ is an identity map (similar to the restriction we make in the previous example of choice problems).

> **Definition**
> - A **retraction** for $f$ is a map $r: B \to A$ for which $r \circ f = 1_A$
> - A **section** for $f$ is a map $s: B \to A$ for which $f \circ s = 1_B$

<center><img alt="cat" src="https://i.ibb.co/Y4tdvMVg/retraction-section-problem-drawio.png" width="75%" /></center>

The two figures above seem really confusing. the section problem looks exactly the same as the retraction problem except for a 180 degree rotation. We can clarify this by letting the sets below be what we *focus*. From the figure on the left, elements of $A$ are sent to $B$ by $f$ and then go back to $A$ with $r$, which is equivalent to an identity map on $A$. From the one on the right, elements of $B$ are sent to $A$ by $s$ and then go back to $B$ with $f$, which is also equivalent to an identity map on $B$.

To better understand this, we divide an identity map with $B$ into two maps $f$ and $g$ for which $g \circ f = 1_A$. We call the first half $f$ *a section for $g$* and the second half $g$ *a retraction of $f$*.

$$
f: A \to B, \quad g: B \to A
$$

It's just like Doraemon's Time Wrap. With the wrap, a section is to convert paper($A$) into wood($B$) and a retraction is transforming back the wood($B$) to paper($A$).

> **Proposition 1**  
> If a map $f: A \to B$ has a section, then for any $T$ and any map $y: T \to B$, there exists a map $x: T \to A$ for which $f \circ x = y$.
>
>**Proof:**  
> Propositions on maps are apt to be proved with *external diagrams*. According to the conditions we can draw the following graph:
> <center><img alt="cat" src="https://i.ibb.co/5xMtxdCt/Proof1.png" width="38%" /></center>
>
> We let $s$ be the section for $f$. From the diagram, we can easily denote $x$ by $x = s \circ y$, then substituting $x$ into the conclusion:
> $$
> \begin{split}
> f \circ x& = f \circ (s \circ y) \\
&= (f \circ s) \circ y \\
&= 1_B \circ y \\
&= y
> \end{split}
> $$

There is actually a concept implied by this proposition, called **surjective**. For example, if $f$ is said to be surjective, each element of its codomain is pointed to by at least one arrow from its domain. If the domain is all the mail trucks and the codomain is all the houses in a block, it is surjective that every house gets at least one piece of mail.

Implied by the proposition, $f$ is actually surjective for maps from $T$. Why? In category theory, we can't just claim $f$ is surjective. Instead we may need a "test object" to test surjectivity of $f$. Let's check it out:

> "For any $T$ and for any $y:T\to B$"

This actually quantifies all possible maps that point to $B$, the codomain of $f$. If $f$ passes the test for every possible $T$, then $f$ is surjective

> ", there exists a map $x: T \to A$ for which $f \circ x = y$"

This is the "test". We can always find a map from $T$ to $A$ such that the diagram becomes commuted. We can prove $f$ is surjective by contradiction.  
If $f$ is not surjective, then there exists some element $b \in B$ that is not in the image of $f$. But then, taking $T$ to be a one-element set and $y: T \to B$ to be the constant map sending the single element to $b$, we cannot find any $x: T \to A$ such that $f \circ x = y$. This is because no matter what $x$ we choose, $f \circ x$ will never hit the element $b$ (since $b$ is not in the image of $f$). This contradicts our assumption that $f$ has a section, which guarantees that such an $x$ always exists. Therefore, $f$ must be surjective.
<center><img alt="cat" src="https://i.ibb.co/39hPtX9B/proof-proposition1-drawio.png" width="80%" /></center>

> A section $s$ for a map $f$ is often thought of as a "choice of representatives". For example if $A$ is the set of all US citizens and $B$ is the set of all congressional districts, then a map $f$ such as
> $$ f : A \to B= residence $$
> divides the people up into clusters, all of those residing in a given district $y$ constituting one cluster. If $s$ means the congressional representative choice, then the condition $f \circ s = 1_B$ means that the representative of district $y$ must reside in $y$. Clearly, there are theoretically a very large number of such choice maps $s$ unless there happens to be some district which is uninhabited, in which case there will be no such maps $s$, as follows from Proposition 1.

Also there is a dual proposition about Proposition 1.

> **Proposition 1\***  
> If a map $f: A \to B$ has a retraction, then for any $T$ and any map $g: A \to T$, there exists a map $x: B \to T$ for which $t \circ f = g$.
>
>**Proof:**   
> We can also draw the external diagram of this proposition below:
> <center><img alt="cat" src="https://i.ibb.co/MkX2QpQ1/Proposition1-drawio.png" width="38%" /></center>
>
> We let $r$ be the retraction of $f$. From the diagram, we can easily denote $t$ by $t = g \circ r$, then substituting $t$ into the conclusion:
> $$
> \begin{split}
> t \circ f& = (g \circ r) \circ f \\
&= g \circ (r \circ f) \\
&= g\circ 1_A \\
&= g
> \end{split}
> $$

There is another useful proposition about retractions:
> **Proposition 2**  
> If a map $f: A \to B$ has a retraction, then for any $T$ and for any pair of maps $x_1\:x_2 : T \to A$ from any set $T$ to $A$
> $$ \text{if}\; f \circ x_1 = f \circ x_2 \; \text{then} \; x_1 = x_2$$
> **Proof:**  
> By our pro forma tactic, we first draw the external diagram:
> 
> 
> $$
> \begin{split}
&x_1 = 1_A \circ x_1 = (r \circ f) \circ x_1 = r \circ (f \circ x_1) \\
\overset{f \circ x_1 = f \circ x_2}{\Longrightarrow}
&r \circ (f \circ x_2) = (r \circ f) \circ x_2 = 1_A \circ x_2 = x_2
> \end {split}
> $$

> **Definition**
> We say any maps ($f$) that satisfy the conclusion of *Proposition 2* to be **injective for maps from $T$**. If $f$ is injective for maps from any $T$, we say $f$ to be **injective** or a **monomorphism**.  

An injective map is a one-to-one map, which is all the elements of its codomain are pointed to by only one arrow from its domain. For example, A map from student identity to students numbers is injective for there is no student who has no identity number or multiple numbers. An injective map also implies the map to be surjective.

Then why the proposition can mean $f$ is one-to-one? It's easy to recall the element-based standard definition of injective functions
> A function f is **injective** if, for any two elements $a_1$ and $a_2$ in set $A$, if $f(a_1) = f(a_2)$, then it must be that $a_1 = a_2$

Let's go back to our categorical definition. The key to understand it is still to introduce a test set $T$. Letting $T$ be a one-element set denoted by $\{\cdot\}$ and $x_1$ $x_2$ be a pair of constant maps which always produce $a_1$ and $a_2$ respectively. Then we have
- $x_1(\cdot) = a_1$
- $x_2(\cdot) = a_2$

The special choice of $T$ and the pair of maps is just a way to pick an element.
- $(f \circ x_1)(\cdot) = f(x_1(\cdot)) = f(a_1)$
- $(f \circ x_2)(\cdot) = f(x_2(\cdot)) = f(a_2)$

So "$f \circ x_1$" is the same as saying "$f(a_1) = f (a_2)$". Similarly, since the maps are just to pick an element from A, $x_1 = x_2$ just means they must pick the same element, whereas the same as saying $a_1 = a_2$.

Also, this can have the dual proposition:

> **Proposition 2\***  
> If a map $f: A \to B$ has a retraction, then for any $T$ and for any pair of maps $t_1\:t_2 : B \to T$ from any set $B$ to $T$
> $$ \text{if}\; t_1 \circ f = t_2 \circ f \; \text{then} \; t_1 = t_2$$
>
> **Proof:**  
> Trivial.

> **Definition**
> We say any map $f$ that satisfy "if $t_1 \circ f = t_2 \circ f$ then $t_1 = t_2$" for every $T$ to be **epimorphism**.

Both monomorphism and epimorphism are "*cancellation properties*". Letting $r$ and $f$ be two maps while $r$ is a retraction of $f$ and $f$ is also a section for $r$  ($r \circ f = 1_A$). What a pair of sets $A$ and $B$ can hold the two maps mentioned? Actually we can roughly think $A$ is *smaller* than $B$. On this point we will talk more precisely below.

> **Proposition 3**  
> If $f: A\to B$ has a retraction and $g:B\to C$ also has a retraction, then $g \circ f : A \to C$ has a retraction.
> **Proof**  
> This can be seen as transitivity of "having a retraction".  
> The condition can be interpreted as 
> $$ 
r_1 \circ f = 1_A \\
r_2 \circ g = 1_B
$$
> The goal is to prove there exists a $r$ for which $r \circ (g \circ f) = 1_A$
> <center><img alt="cat" src="https://i.ibb.co/KcV8RM6y/Proposition3-proof-drawio.png" width="50%" /></center>
>
> $$
\begin{split}
r \circ (g \circ f) &= (r_1 \circ r_2) \circ (g \circ f) \\
&= r_1 \circ (r_2 \circ g) \circ f = r_1 \circ 1_B \circ f \\
&= r_1 \circ f = 1_A
\end{split}
$$

It also has a dual proposition on the transitivity of "having a section".

> **Definition**  
> An endomap (whose domain and codomain are the same set) $e$ is called idempotent if $e \circ e = e$

We can actually conclude the following lemmas from the proposition

> Taking $r$ to be a retraction of $f$, then $f \circ r$ is an *idempotent*

**Proof**: Letting $e = f \circ r$, the goal is to prove $e \circ e = e$. We can extend this to $f \circ r \circ f \circ r$.

$$
e \circ e = f \circ (r \circ f) \circ r = f \circ 1_A \circ r = f \circ r = e
$$


> Taking $r$ to be a retraction of $f$, if $f$ is an isomorphism, then $f \circ r$ is an identity

**Proof**: Let $f : A\to B$ and hence $r : B \to A$. Due to the condition, we have $r \circ f = 1_A$. The goal is to prove $f \circ r = 1_B$. Since $f^{-1} \circ f = 1_A$, then $f^{-1} \circ f = r \circ f$. Finally we have $r = f^{-1}$ which is sufficient for $f \circ r = 1_B$.

> **Theorem _(Uniqueness of inverses)_**  
> If $f$ have both a section $s$ and a retraction $r$, then $r = s$
>
> **Proof:**  
> From the proposition, we know that $f \circ s = 1_B$ and $r \circ f = 1_A$. Then we can construct $s$ from $r$ by applying an identity and extending it:
$$
r \circ 1_B = r \circ f \circ s = 1_A \circ s = s
$$

The theorem implies $f$ can only have one unique inverse $f^{-1}$ and the inverse is both a section and retraction of $f$. From this point, we can just redefine *Isomorphism* with sections and retractions.

> **Definition**  
> A map $f: A \to B$ is called an **isomorphism** or invertible map , if there exists a map $f^{-1}$ which is both a section and retraction for $f$.

We can derive some useful lemmas from what we have proved.

> If $f$ and $g$ are both isomorphisms, then $g \circ f$ is isomorphism too.

**Proof:** Let $f : A\to B$, $g: B \to C$. The goal is to prove $g \circ f$ have both a retraction $r$ and a section $s$. The condition implies the following equations:

$$
\begin{split}
1_A &= f^{-1} \circ f \quad \\
1_B &= f \circ f^{-1} = g^{-1} \circ g \\
1_C &= g \circ g^{-1}
\end{split}
$$

Then we construct the retraction from $1_A$
$$
\begin{split}
1_A &= f^{-1} \circ f \\
&= f^{-1} \circ 1_B \circ f \\
&= f^{-1} \circ (g^{-1} \circ g) \circ f \\
&= (f^{-1} \circ g^{-1}) \circ (g \circ f) \\
r &= f^{-1} \circ g^{-1}
\end{split}
$$
Similarly, we construct the section from $1_C$

$$
\begin{split}
1_C &= g \circ g^{-1} \\
&= g \circ 1_B \circ g^{-1} \\
&= g \circ (f \circ f^{-1}) \circ g^{-1} \\
&= (g \circ f) \circ (f^{-1} \circ g^{-1}) \\
s &= f^{-1} \circ g^{-1}
\end{split}
$$

> $(g \circ f)^{-1} = f^{-1} \circ g^{-1}$

**Proof:** Proved above.
