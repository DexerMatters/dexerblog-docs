## Introduction
Modal logics are formal logic systems which contains *modal operators* such as $\Box$ and $\lozenge$, representing statements about their *necessity* and *possibility*. For instance, the formula $\Box P$ can represent "necessarily P" while $\lozenge P$ "possibly P".

The most important initiative of contextual modal logics is to allow proposition itself to speak about contexts, introducing contexts tied to every proposition and indicating when or where a proposition is true or valid, Hence we make the annotation $[\Psi]P$ introducing a contextual modal. In programming language design, contextual modal logics are the foundation of staged computation where codes are sequentially divided into several stages. It can formally expects in which context or world a term is sound.

## Categorical Judgments
We say that the proposition $A$ is categorically true or *valid* if it does not depend on any hypotheses about the truth of other proposition. However, it may depend on the validity of other proposition. We write $P\:\mathtt{valid}$ in contrast with $P \: \mathtt{true}$ to represent $P$ is valid.

Compared with the assumption list $\Gamma$, we write $\Delta$ as a list of assumptions on the validity of propositions. Then $u$ represents *meta-variable* bound with a valid proposition in $\Delta$.

$$
\begin{split}
\Gamma :=& x:A\:\mathtt{true}, \Gamma \\
\Delta :=& u::A\:\mathtt{valid}, \Delta
\end{split}
$$

Since validity does not depend on $\Gamma$ and may depend on the validity of other proposition. We can write the following rule as the definition of validity without effort:

$$
{
\Delta; \cdot \vdash A\:\mathtt{true}
\over
\Delta; \Gamma \vdash A\:\mathtt{valid}
}
\mathtt{vld}
$$

Then we can use the assumption about the validity of $A$ in $\Delta$

$$
{
\over
\Delta, u::A\:\mathtt{valid}, \Delta'; \Gamma \vdash A\:\mathtt{true}
}
\mathtt{vldhyp}_u
$$

Another way to think this definition is to use the multiple-world semantics. Validity of $A$ can be assumed only if $A$ is true in every possible world. Since we can never circumscribe all possible worlds, the definition is equivalent when $A$ is true without any assumption. Therefore we require that A must be true in a world about which we assume nothing ($Γ = \cdot$) and thereby the assumption can be weakened into anything while $A$ is still true, except for the propositions we assumed to be always true ($∆$).

## Make Validity Relative
Since the former paragraph we annotate $A\:\mathtt{valid}$ for $A$ is true in all possible worlds, we write $A\: \mathtt{valid}[\Psi]$ if $A$ is true in every world in which assumptions in $\Psi$ are true. $\Psi$ is also a labeled list about assumptions ($y_0:B_0\:\mathtt{true}, y_1:B_1\:\mathtt{true}, ...$). So that $\Delta$ now is formed with labelled relative validity propositions.

$$
\begin{split}
\Gamma :=& x:A\:\mathtt{true}, \Gamma \\
\Psi   :=& y:B\:\mathtt{true}, \Psi \\
\Delta :=& u::A\:\mathtt{valid}[\Psi], \Delta
\end{split}
$$

In order to prove $A\:\mathtt{valid}[\Psi]$, we prove $A$ is true only with the assumptions provided by $\Psi$ because the truth of $A$ is just relative to $\Psi$. Then we can easily write the definition of contextual validity:

$$
\Delta; \Psi \vdash A\:\mathtt{true}
\over
\Delta; \Gamma \vdash A\:\mathtt{valid}[\Psi]
$$

Remember the definition? We need guarantee all the assumptions in $\Psi$ are true using the assumptions in $\Gamma$:

$$
{\Delta; \Gamma \vdash B_1 \: true \quad ... \quad B_m \: true
\over
\Delta; \Gamma \vdash \Psi}
\mathtt{ctx}
$$

Thereby the hypothesis we mention above is modified into the rule below:

$$
{
\Delta, u::A\:\mathtt{valid}[\Psi], \Delta'; \Gamma \vdash \Psi
\over
\Delta, u::A\:\mathtt{valid}[\Psi], \Delta'; \Gamma \vdash A\:\mathtt{true}
}
\mathtt{ctxhyp}_u
$$

This can be explained as $A$ is true only if all the assumptions in its relevant context ($\Psi$) are true. It is obvious that the rule $\mathtt{vldhyp}_u$ above is the special case of $\mathtt{ctxhyp}_u$ by letting $\Psi$ be empty:

$$
{
\Delta, u::A\:\mathtt{valid}[\cdot], \Delta'; \Gamma \vdash \cdot
\over
\Delta, u::A\:\mathtt{valid}[\cdot], \Delta'; \Gamma \vdash A\:\mathtt{true}
}
\mathtt{ctxhyp}_u
$$

Then, whether $A$ is true is independent to the context $\Gamma$ and only determined by the validity of $A$ which we assume as the meta-variable.

## Finally, Contextual Modal Necessity
