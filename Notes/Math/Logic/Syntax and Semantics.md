# Syntax and Semantics In Propositional Logic

Modern logic is a symbolic system, which warrants a formal language to describe logic in a general and abstract way. Therefore, like linguistics, we have to define syntax which defines the symbols and guarantees them well formed, and also semantics which assigns the meaning of syntactic structures.

## Syntax
> In logic, syntax is an arrangement of well-structured entities in the formal languages or formal systems that express something. Syntax is concerned with the rules used for constructing or transforming the symbols and words of a language, as contrasted with the semantics of a language, which is concerned with its meaning.   -- Wikipedia

In propositional logic, we define a (countably infinite) set of alphabet strings $\{p_i | i \in \N\}$ as *propositions* $\mathsf{Props}$. The *formulas* of propositional logic extend the set of propositions to $\mathsf{Props} \cup \{(, ), \to, \bot\}$. 

To ensure formulas are well formed, we have to define rules:

$\bold{Definition\:1.}$ The set of well-formed formulas in propositional logic is the smallest set satisfying the following properties.
- $\bot$ is well formed.
- For every $i \in \N$, $p_i$ is well formed.
- If any two well-formed formulas $\varphi$ and $\psi$ are well formed, then $(\varphi \to \psi)$ is well formed.

We can also define the rules with *Backus-Naur Form* (BNF).

$$
\begin{align*}
&\text{Formula} \quad &&\varphi ::= p \mid \bot \mid (\varphi \to \psi) \\ 
\end{align*}
$$

For other operators in propositional formulas like conjunction, disjunction, and negation, we can also define them in a similar way.

$$
\begin{align*}
&\text{Formula} && \varphi ::= p \mid \bot \mid (\varphi \to \psi) \\
&&& \quad \quad \mid (\varphi \land \psi) \mid (\varphi \lor \psi) \mid \neg \varphi
\end{align*}
$$

Here, $\land$, $\lor$, and $\neg$ represent conjunction, disjunction, and negation, respectively. These additional operators extend the expressive power of propositional logic while maintaining the syntactic structure.

## Semantics

We will now provide semantics to formulas, the meaning of formulas. The semantics of formulas in a logic are typically defined with respect to a *model*, which identifies a "world" or "situation" where some facts are true. 

This world or model is a truth validation or assignment that assigns truth values (1 = true and 0 = false) to every proposition.

$\bold{Definition\:2.}$ A model (truth assignment) is a function $\mathsf{v}$ that maps each position to their truth values, i.e., $\mathsf{v} : \mathsf{Prop} \to \{0, 1\}$.

We will define the semantics through a satisfaction relation, which is a binary relation $\vDash$ between valuations and formulas. The statement $\mathsf{v} \vDash \varphi$ should be read as “$\mathsf{v}$ satisfies $\varphi$” or “$\varphi$ is true in $\mathsf{v}$" or “$\mathsf{v}$  is a model of $\varphi$”. We also say $\mathsf{v} \nvDash \varphi$ when the relation does not hold.

$\bold{Definition\:3.}$ For a well formed formula $\varphi$ and model or validation $\mathsf{v}$, the relation $\mathsf{v} \vDash \varphi$ is defined inductively as follows.
- $\mathsf{v} \vDash \bot$ iff $\mathsf{v} \nvDash \bot$ for $\bot$ (false) can never be validated.
- $\mathsf{v} \vDash p$ if $\mathsf{v}(p) = 1$.
- $\mathsf{v} \vDash (\varphi \to \psi)$ if either $\mathsf{v} \vDash \psi$ or $\mathsf{v} \nvDash \varphi$.