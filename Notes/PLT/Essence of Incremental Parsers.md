# Background
Nowadays, incremental language servers, type checker and structural editor have achieved distinctive progress on improving developers' coding experience. Editors like what Hazel has made unveil the great extensibility and versatility of programming in an incremental way. This article shows an efficient approach on modern parser design with inspirations from previous work.

# Grammar
Grammar is a set of named rules for lexers and parsers to automatically apply to the input and transform it into a structure. 

## Left Recursion Problem
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
1. **Early termination**
2. **No termination**
3. **Good termination**