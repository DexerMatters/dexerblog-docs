## 1 Isomorphisms

> **Definition**
> A map $A \overset{f}{\longrightarrow} B$ is called an **isomorphism** or invertible map , if there exists a map $B \overset{g}{\longrightarrow} A$ that holds $f \circ g = 1_B$ and $g \circ f = 1_A$. We also say $A$ and $B$ are **isomorphic** if there is at least one isomorphism between them.

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

### 2 Retractions, Sections and Idempotents
In the previous chapter, we talk about determination and choice problems, while retractions and sections are just special cases where $h$ is an identity map (similar to the restriction we make in the previous example of choice problems).

> **Definition**
> - A **retraction** for $f$ is a map $B \overset{r}{\longrightarrow} A$ for which $r \circ f = 1_A$
> - A **section** for $f$ is a map $B \overset{s}{\longrightarrow}A$ for which $f \circ s = 1_B$

<center><img alt="cat" src="https://i.ibb.co/Y4tdvMVg/retraction-section-problem-drawio.png" width="75%" /></center>

The two figures above seem really confusing. the section problem looks exactly the same as the retraction problem except for a 180 degree rotation. We can clarify this by letting the sets below be what we *focus*. From the figure on the left, elements of $A$ are sent to $B$ by $f$ and then go back to $A$ with $r$, which is equivalent to an identity map on $A$. From the one on the right, elements of $B$ are sent to $A$ by $s$ and then go back to $B$ with $f$, which is also equivalent to an identity map on $B$.

To better understand this, we divide an identity map with $B$ into two maps $f$ and $g$ for which $g \circ f = 1_A$. We call the first half $f$ *a section for $g$* and the second half $g$ *a retraction for $f$*.

$$
A \overset{f}{\longrightarrow} B \overset{g}{\longrightarrow}A
$$

It's just like Doraemon's Time Wrap. With the wrap, a section is to convert paper($A$) into wood($B$) and a retraction is transforming back the wood($B$) to paper($A$).

