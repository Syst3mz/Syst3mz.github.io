+++
title = "LR & LALR Parsing"
date = 2022-12-18
weight = 3
+++


This page assumes you have read at least [intro to parsing](@/intro-to-parsing.md) or know what parsing is.
LR and by extension LALR parsers are a type of parser which creates the **AST** bottom up, and reads the input left-to-right.

## A Quick Note on Notation
```ebnf
S -> A "c"
A -> "ab"
```

What are you looking at? In very brief, above is a _grammar_. Grammars define a _language_, and a language is the set of all strings
that fit a grammar. Read it from top-to-bottom and left to right.
1. `S` is a "non-terminal", meaning it's not a string literal.
2. `->` says "Wherever you see what precedes me, you can replace it with what succeeds me."
3. `A` is another "non-terminal". A non-terminal on the right side of an arrow shows the power of grammars. Grammars alllow embedding non-terminals within non-terminals, to create more complex languages.
4. `"c"` a string literal, the only kind of "terminal" that exists.  Additionally, its position after `A` indicates that `"c"` must follow a `A`.


One important assumption made is that any amount of whitespace may occur between parts of a non-terminal on the right side of an arrow.

See if you can determine if the following strings fit in the language defined above. Click the following strings below to see if they are in the language.
<details>
<summary>"abc"</summary>
✅ Yep, this one works
</details>
<details>
<summary>"abcc"</summary>
❌ Nope, the second c makes this string not part of the language defined above.
</details>
<details>
<summary>"ababc"</summary>
❌ Nope, the second ab makes this string not part of the language defined above.
</details>
<details>
<summary>"ab c"</summary>
✅ Yep, this one works. Remember that for this notation any amount of whitespace may follow.
</details>




## Bottom up
Just what does it mean to make the **AST** from the bottom up? Check back later for more information!
