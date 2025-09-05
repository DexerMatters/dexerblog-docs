# I - Isomorphisms
## Definition
A map $A \overset{f}{\longrightarrow} B$ is called an **isomorphism** or invertible map , if there exists a map $B \overset{g}{\longrightarrow} A$ that holds $f \circ g = 1_B$ and $g \circ f = 1_A$. We also say $A$ and $B$ are **isomorphic** if there is at least one isomorphism between them.

We also call the $g$ we mention above the inverse map of $f$, denoted by $f^{-1}$.

## Properties
Isomorphism has *reflexivity*, *symmetry*, *transitivity*. 

## Notes
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

The two cases are actually analogous to the *termination* and *choice* problems (we call them *general division problems*) about maps.

1. **Determination Problem**
Given two maps $f$ and $h$ as shown below, 

<img alt="cat" src="https://i.ibb.co/qYRkwThC/determination-problem-drawio.png" width="28%" />