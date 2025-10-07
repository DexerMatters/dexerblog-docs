# Background
Nowadays, incremental language servers, type checker and structural editor have achieved distinctive progress on improving developers' coding experience. Editors like what Hazel has made unveil the great extensibility and versatility of programming in an incremental way. This article shows an efficient approach on modern parser design with inspirations from previous work.

# Grammar
Grammar is a set of named rules for lexers and parsers to automatically apply to the input and transform it into a structure. 

## Termination Problem
If you are a fresh learner on compilers, you may write your own BNF terms like below:

$$
\begin{split}
\text{expr} &::= \text{add} \: | \: \text{number} \\
\text{number} &::= \text{\color{green}{/[0-9]+/}} \\
\text{add} &::= \text{expr} \; \text{\color{green}{'+'}} \; \text{expr}
\end{split}
$$

For convenience, lexer rules are colored with green and `expr` is always the entrance of parsing. Now think about the expression "1 + 1". We need to read it from left to right to parse it properly.
- `expr` is matched. It has two possible choices `add` and `number`. Now try to apply `add`.
- `add` is matched. It is a sequence of multiple subrules. Now try the leftmost one `expr`.
- `expr` is matched. ...


You can easily notice that the grammar is malformed as it brings infinite steps of parsing. To fix this, we can just change the order of the choices to $\text{number} \: | \: \text{add}$. 

- `expr` is matched. Instead of the previous choice `add`, let's apply `number`
- `number` is matched and the parsing terminates

Even though it doesn't cause an endless loop, the parser ends earlier than expected. We can conclude that your arbitrary BNF terms can produce the following results if you mind the time to have a second look.
1. **Early termination** There is still a remaining text which is matched but unable to be parsed.
2. **No termination** The parser is stuck among some steps and can never find a terminal rule.
3. **Good termination** All the matched characters are parsed and abide by the grammar rules.

If you are a skilled grammar designer, you write rules and avoid the left recursion subconsciously. You know that addition, in common sense, is *left-associative*, which means addition prefers to prioritize its left hand expression. For example, the term "1 + 2 + 3 + 4" can be rewritten as "((1 + 2) + 3) + 4". You can note that the leftmost term is always a terminal (number). Congratulations, you find a proper way to deal with the termination problem by simply changing the last rule to $\text{add} ::= \text{number} \; \text{\color{green}{'+'}} \; \text{expr}$. 

- `expr` is matched. It has two possible choices `add` and `number`. Now try to apply `add`.
- `add` is matched. It is a sequence. Let's match `number` first, then `+` and `expr`.
- `number` is matched. `1` is consumed and `+ 1` is left.
- `+` is matched and consumed and `1` is left.
- `expr` is matched. Now try to apply `add`. But there is no `+` left in the remaining text, fail back to `number`.
- `number` is matched and consumed. End of input is met and no error raised.

However, if you are proficient enough and you know the cost of recursion in a program, you will probably to choose *Extended Backus–Naur form*(EBNF) to write grammars. EBNF mainly extends BNF with two additional notations, namely option and repetition, written as `[]` and `{}` respectively. This can help you avoid recursion with a more deterministic way and reduce complexity. Finally, we get this:

$$
\begin{split}
\text{expr} &::= \text{number} \; \{\text{\color{green}{'+'}} \; \text{number}\} \\
\text{number} &::= \text{\color{green}{/[0-9]+/}}
\end{split}
$$

> Wait! This is not equivalent to the previous form. For example, the final abstract tree with the previous rules is like
> ``` 
> expr
> | add
> | | number
> | | '+'
> | | expr
> | | | number
> ```
> But with the current solution, it should be like
> ``` 
> expr
> | number
> | '+'
> | number
> ```
> Umm. Don't be so eager about pointing them out. We will talk about trivia in parser design.

This method actually hands over associativity to the higher level but it does make a beautiful and pruned tree. With this approach, there are no recursion, associativity, termination problems any more. Now let's extend our ideas to a complete grammar for arithmetic expressions.

$$
\begin{split}
\text{expr} &::= \text{mul} \; \{\text{\color{green}{'*'}} \; \text{mul}\} \\
\text{mul} &::= \text{factor} \; \{\text{\color{green}{'+'}} \; \text{factor}\} \\
\text{factor} &::= \text{number} \; | \; \text{\color{green}{'('}} \; \text{expr} \; \text{\color{green}{')'}} \\
\text{number} &::= \text{\color{green}{/[0-9]+/}}
\end{split}
$$

You can find that there is a recursion in the rule `factor`. However, this sort of recursion can be proved halting. Loosely speaking, since a terminal `'('` is on the left of `expr`, the parser must read one left parenthesis before reaching `expr`. Terminals are terms with fixed sizes that always terminate and there can not be infinite left parentheses in any text. Therefore, the recursion can terminate.

A counterexample can be the first form in this session. In `add`, the first subrule the parser meet is not a terminal but `expr`. We can't know the exact steps or length of the rule `expr`. So the parser gets stuck there and never halts. Another case is the second form, in which we swap `add` and `number` to make the parser terminate. There is `number` as the first rule in `expr` so `expr` can terminate as long as a number is met, and thus `add` can terminate too.

We call it **left recursion** when the leftmost subrule in a rule is recursive (There is the rule itself in its leftmost subrule). The left recursion can terminate only when the leftmost subrule has no left recursion. In short, while tracing every leftmost rules in a grammar, we must find a terminal, otherwise parser unable to halt.