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

We call it **left recursion** when the leftmost subrule in a rule is recursive (There is the rule itself in its leftmost subrule).Left recursion can terminate only when the leftmost subrule has no left recursion. In short, while tracing every leftmost rules in a grammar, we must find a terminal at most, otherwise parser unable to halt.


### A Checker for Left Recursion

In this article, all codes are presented in Haskell for conciseness and correctness, including grammar notations.

```haskell
data Grammar = Grammar [(String, Rule)]
data Rule
  = Prod [Rule]
  | Ref String
  | Terminal
```

We don't need to distinguish sequences and choices in this scenario, thus using `Prod` standing for both. Terminals are atomic rules that consume characters and stop the parser immediately, which is also presented as `Terminal` for all cases, Therefore, we write codes according to the first grammar we design.

```Haskell
grammar1 :: Grammar
grammar1 =
  Grammar
    [ ("expr", Prod [Ref "add", Ref "number"]),
      ("add", Prod [Ref "expr", Terminal, Ref "expr"]),
      ("number", Terminal)
    ]
```

Insight for this checker is a special depth-first traverse which only accesses the leftmost node. This checker is applied to each rule in a grammar and show if a rule reference is pointing to the same name of the rule that the traverse starts at.

```haskell
checkLeftRecursion :: Grammar -> [(String, Rule)]
checkLeftRecursion ctx = filter (uncurry (isLeftRecursive ctx)) (unGrammar ctx)

isLeftRecursive :: Grammar -> String -> Rule -> Bool
isLeftRecursive ctx name rule = case rule of
  Prod (leftmost : _) -> isLeftRecursive ctx name leftmost
  Ref refName | refName /= name -> case lookup refName (unGrammar ctx) of
    Nothing -> False
    Just refRule -> isLeftRecursive ctx name refRule
  Ref refName | refName == name -> True
  _ -> False
```

### Solve Early Termination

Early termination happens in some step of parsing when two choices (subrules in a choice notation) can both be satisfied with the input while the one first applied consumes less characters than another one. This causes the parser to stop at the first one and abandons remaining characters even though they are valid.

One solution is done by adjusting the order of two rules, making the rule that consumes more the first. This is the most preferred way and accustoms grammar designers to care more about viability. Is early termination a bug to our parser? No. It is just an expected or unexpected result that designers contribute. Alternatively, by combine end-of-input rule with the first rule into a sequence, the parser checks whether end of input is met after finished the first rule. It autonomously falls back to the second one if the end is unmet. And moreover, we can make choices always evaluated (paralleled, even concurrently) and compare length of the text consumed by each choice...

There are a plenty of candidates to perfectly solve this problem. Some parser libraries also use *control flow combinator* to acknowledge the exact parsing logic of the grammar. For example, a `choice` combinator $A \;or\; B$ is similar to choice notations we used before, but there is no fall back on input texts. For example, let $A$ be a sequence $\text{number} \; '.'$ and $B$ be a terminal $'*'$. The input text "42*" is valid because when "\*" failed to satisfy $'.'$ the text does not fall back to the state when $A$ is not applied but remains "\*" (the state that "42" is consumed). $A$ fails and leaves "*" for $B$ and finally $B$ is satisfied and ends the parsing. A `try` combinator $try \: A$ controls the parser to recover the consumed text if $A$ fails. Then we can know that $(try \; A)\;or\;B$ is equivalent to $A\;|\;B$. This hands over the mental burden of falling back to designers, letting them to decide whether to use $try$. 

Here we introduce precedence to solve this. This can also automatically eliminate endless left recursions.

## Precedence and Normalization
