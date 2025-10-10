# Background
Nowadays, incremental language servers, type checker and structural editor have achieved distinctive progress on improving developers' coding experience. Editors like what Hazel has made unveil the great extensibility and versatility of programming in an incremental way. This article shows an efficient approach on modern parser design with inspirations from previous work.

# Grammar
Grammar is a set of named rules for lexers and parsers to automatically apply to the input and transform it into a structure. 

## Termination Problem
If you are a fresh learner on compilers, you may write your own BNF terms like below:

$$
\begin{split}
\text{expr} &::= \text{add} \mid \text{number} \\
\text{number} &::= \text{\color{green}{/[0-9]+/}} \\
\text{add} &::= \text{expr} \; \text{\color{green}{'+'}} \; \text{expr}
\end{split}
$$

For convenience, lexer rules are colored with green and `expr` is always the entrance of parsing. Now think about the expression "1 + 1". We need to read it from left to right to parse it properly.
- `expr` is matched. It has two possible choices `add` and `number`. Now try to apply `add`.
- `add` is matched. It is a sequence of multiple subrules. Now try the leftmost one `expr`.
- `expr` is matched. ...

You can surely find your grammar form an endless loop which makes the parser stuck and never gives a result. If you are not a fresh learner, you know that `add` is **right-associative**. That is the expression "1 + 2 + 3 + 4" can always be rewritten as "((1 + 1) + 1) + 1". And you notice that the left hand of the operator is always a number. Now we can confidently present the final repaired grammar: 

$$
\begin{split}
\text{expr} &::= \text{add} \mid \text{number} \\
\text{number} &::= \text{\color{green}{/[0-9]+/}} \\
\text{add} &::= \text{expr} \; \text{\color{green}{'+'}} \; \text{number}
\end{split}
$$

Wait. There is still `expr` on the left hand. Since the parser is from left to right, it will still become stuck between `add` and `expr`, right? It seems so. However, it can terminate well in fact, because there is the terminal `number` on the right hand like a gatekeeper. "From left to right" is simply the direction in which input is consumed. 

### Left Recursion

We call our parser that operates "from left to right" a **naive recursive-descent parser** and call **left recursion** to a loop where a naive recursive-descent parser gets stuck. Moreover, we also call **terminals** or **tokens** to atomic rules that stop the parser immediately.

How can we know that a rule reference is left-recursive? This is actually really simple and takes $O(n)$ time complexity where $n$ is depth of the recursion. It is essentially a leftmost depth-first traverse.
1. Initialize a list for the visited nodes
2. Visiting the rule reference named $l$. Mark it as visited and let the referenced rule be $r$ if the first time, or return $l$ and end if already visited once.
3. Checking the rule $r$:
    - Case 1 - A terminal: Return an empty string.
    - Case 2 - A sequence: Restart Step 3 where $r$ equals to the leftmost rule in the sequence
    - Case 3 - A choice: Restart Step 3 where $r$ equals to the leftmost rule in the choice.
    - Case 4 - A reference: Restart Step 2 where $l$ equals to the name of the reference.

We can apply the algorithm to every single named rules in a grammar and get a list of left-recursive rules. Letting $A$ be an arbitrary left recursive rule, it holds the following useful properties:

- For any rule $B$, $A \mid B$ is left-recursive.
- For any rule $B$, $A \; B$ is left-recursive.

There must be a terminal for each left recursion



### Insight 1: Refine the Grammar
By swapping `expr` and `number` besides `'+'`, we turn the original left recursion to a right one that allows the parser takes straight forward steps without specification. 

$$
\begin{split}
\text{add} &::= \text{number} \; \text{\color{green}{'+'}} \; \text{expr}
\end{split}
$$

For this time, when the parser enters `add`, it is no longer trapped in a loop. We test "1 + 1" with this approach.

1. `expr` is matched. It has two possible options `add` and `number`. Now try to apply `add`.
2. `add` is matched. It is a sequence of multiple subrules. Now try the first one `number`.
3. `number` is matched and consumed. Now try the second one `+`
4. `+` is matched and consumed. Now try the last one `expr`
5. `expr` is matched. Now try the first choice `add`
6. `number` is matched and consumed. `+` fails (unexpected end of input), thus falling back to the second choice `number` in Step 5
7. `number` is matched and consumed. End of input is met. Finish.

We find this can solve the left recursion problem. But it has semantic conflicts with the initial left recursive grammar we have designed. This time `+` behaves like `1 + (2 + (3 + 4))`, a left-associative operator. Expressions are folded from right instead of our initial semantics (That is, it is always the rightmost expression to be evaluated first). Does it really matter about left or right associativity? Number arranged by addition is a group which holds associativity ($(a + b) + c = a + (b + c)$). So this approach is valid for addition. In contrast, number arranged by subtraction has no such axiom. $(a - b) - c$ is definitely different from $a - ( b - c)$. 

Another way to refine the grammar is to use **Extended Backus-Naur Form** (EBNF) to eliminate the left recursion. "1 + 2 + 3 + 4" can be seen as a repetitive pattern `number '+'` and a terminal 'number'. EBNF introduces repetition to BNF so we can easily rewrite `expr` into

$$
\begin{split}
\text{expr} &::= \{ \text{number} \; \text{\color{green}{'+'}} \} \; \text{number}
\end{split}
$$

It looks really elegant with conciseness, and yet also shows the different semantics from the initial idea. A node for addition is a sequence instead of a binary tree. The associativity is also eliminated with this approach, entrusting some stage after the parser to evaluate it with orders. However, it is appreciated to indicate the sufficient semantics in a grammar and stay transparent to any other sessions afterward.

### Insight 2: Use Precedence Levels

From the EBNF way talked in the last session, we can notice some ideas about designing a left-recursive-descent parser. The way introduces repetition to eliminate the recursion. But what if there are two different left-recursive rules and we have to prioritize one over another one (e.t., addition over multiplication)? An explicit precedence is now required to solve the ambiguity.


#### Precedence

Second, we need to know **precedence**. precedence level (or simply, level) specifies which part of a rule is prior to the other part on parsing. Level can be relative, there can be a *lower* or *higher* subrule in a sequence. For example, the rule `add` requires higher level on its left hand and lower one on its right to guarantee right-associativity.

#### Index Levels

There is a rule defined as $R ::= A \mid B \mid C$. $A$ has the highest level in $R$'s scope because it is first evaluated, $B$ follows and $C$ has the lowest. We denote $\overset{n}{A}$ to indicate the level where $n$ is a natural number and $A$ is an arbitrary rule. Then $R ::= \overset{2}A \mid \overset{1}B \mid \overset{0}C$. This denotation is trivial and simply for specification. Then we denote $A:m$ non-trivially to drop options whose level is higher than $m$ in $A$, where $m$ is a natural number. For example, $R:0 = \overset{0}C$,  $R:1 = \overset{1}B \mid \overset{0}C$ and $R:2 = R = \overset{2}A \mid \overset{1}B \mid \overset{0}C$ Then `add` be written like this:
$$
\begin{split}
\text{expr} &::= \overset{1}{\text{add}} \mid \overset{0}{\text{number}} \\
\text{add} &::= \text{expr} : 1 \; \text{\color{green}{'+'}} \; \text{expr}:0
\end{split}
$$

The form distinctively indicates "the rule `add` requires higher level on its left hand and lower one on its right to guarantee right-associativity". Let's extend the grammar for arithmetic expressions.


> Non-choice rules are indexed with 0 as whole.
> Intuitively, $R:m = R$ where $m$ is the largest among all indices in $R$ since it does not drop anything.

$$
\begin{align*}
\text{expr} &::= \;\;\overset{2}{(\text{add} \mid \text{sub})} \: \\
&\qquad | \: \overset{1}{(\text{mul} \mid \text{div})} \: \\
&\qquad | \: \overset{0}{(\text{number}  \mid \text{\color{green}{'('}} \;\text{expr} \; \text{\color{green}{')'}})} \\
\text{mul} &::= \text{expr} : 1 \; \text{\color{green}{'*'}} \; \text{expr}:0 \\
\text{div} &::= \text{expr} : 1 \; \text{\color{green}{'/'}} \; \text{expr}:0 \\
\text{add} &::= \text{expr} \; \text{\color{green}{'+'}} \; \text{expr}:1 \\
\text{sub} &::= \text{expr} \; \text{\color{green}{'-'}} \; \text{expr}:1 \\
\end{align*}
$$

We specify `add` and `sub` at the level 2 (highest), `mul` and `div` at 1, and `number` and parenthesized expression at 0 (lowest).


### Solve it, but Intuitively.

Right recursions are intuitive because the final abstract tree is built from top to bottom. In the example where we let $\text{add} ::= \text{number} \; \text{\color{green}{'+'}} \; \text{expr}$, when the parser enters the rule, a parent node "Add" is formed with a number as its left children. The following step is to parse with the rule `expr` and get the result as the right children. The binary tree "Add" extends itself from top to bottom with all its right branches stretching to the leaf. 

Left recursions are on the contrary. In the example where we let $\text{add} ::= \text{expr} \; \text{\color{green}{'+'}} \; \text{number}$, the parser has to firstly produce the node "Add" as the starting point with a number as right children. Then the node is used to become the left node produced by the second step. The binary tree "Add" extends itself from bottom to top with all its left branches stretching to the root.


<center>
<img src="https://i.ibb.co/q3GLY9zt/rnl-drawio.png" width="50%" alt="rnl-drawio" border="0" />
</center>

First of all. We have to know the leaf of the tree expected to generate. Let's take the rule $\text{add} ::= \text{expr} \; \text{\color{green}{'+'}} \; \text{number}$ for example. The rule intuitively acknowledges us `number` is the "leaf rule". Then execute the parser with $\text{number} \; \text{\color{green}{'+'}} \; \text{number}$ once to get the bottommost node `Add(1, 1)`. Finally repeat the parser with $\text{\color{green}{'+'}} \; \text{number}$ until end of input or errors, and for each time a new `Add` node is created and its left child is the `Add` created at the last time. By accumulating the nodes, a left-recursive tree is built.

This looks like a final solution. But how can we know the "leaf of the tree"? A heuristic approach according to the grammar we write is just to let it be the right hand rule of the operator. But still how can we know the "right hand rule of the operator"? We need still inductively conclude what is an "operator" and what is the "right hand". 

For the first question. The heuristic approach is probably not true. A counterexample can be a left-recursive unary operator such like the pointer types in C "T**". Its syntax rule can be written as $A ::= A \; \text{\color{green}{'*'}} \mid T$ where $T$ is a terminal. There is no "right hand" in this rule. Hence a right hand rule *not necessarily* exists while parsing a left recursive rule.

For the second question. The operator not necessarily exists as well. Think about the rule $R ::= R\;T$. The rule require a token satisfying $T$ on the right of the next token that satisfies the same rule. For all tokens ($t_0, t_1, ... , t_i$ where $i \in \N$) that satisfy $T$, the valid input text is a sequence of $t_0, t_1, ... , t_i$ without a left limit. In practice, the source text can not infinitely stretch leftwards.