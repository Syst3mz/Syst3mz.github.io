+++
title = "Introduction to Parsing"
date = 2023-01-23
weight = 4
+++

Parsing, is the first meaningful step to processing a programming language. But what is parsing?

# Parsing (and Lexing too!)
Parsing, in very brief is taking some string of text and turning it into a something meaningful for the computer.
Most often, this is an **A**bstract **S**yntax **T**ree (**AST**). An **AST** is a tree where all information that is
not relevant to the further processing of some source is removed. Consider the following very complex
expression bellow:
```
1 + (2 * 4)
```
let's first look at what the above expression would appear like as (one) **AST** before we fall down the rabit hole that
gets us there.

[![](https://mermaid.ink/img/pako:eNpVkDEPgjAQhf8KuVEhsQgoDA6GVRfdWoYKBZrQ1tQSQwj_3Vp06E33vnv3krsZatUwKKAd1LvuqTbBvSQysIXwmUuqpyqIolMQ4-soHkxX6zB2cI_RTyOnE0xgS8Bj6T9mhamDmZ-WOXjAsWc64o2nc38pdxDtcFJBCIJpQXljL5m_YwKmZ4IRKGw78K43BIhcrJGORt0mWUNh9MhCGJ8NNazktNNUQNHS4WUpa7hR-rL-play5R0sH4fdVjo?type=png)](https://mermaid.live/edit#pako:eNpVkDEPgjAQhf8KuVEhsQgoDA6GVRfdWoYKBZrQ1tQSQwj_3Vp06E33vnv3krsZatUwKKAd1LvuqTbBvSQysIXwmUuqpyqIolMQ4-soHkxX6zB2cI_RTyOnE0xgS8Bj6T9mhamDmZ-WOXjAsWc64o2nc38pdxDtcFJBCIJpQXljL5m_YwKmZ4IRKGw78K43BIhcrJGORt0mWUNh9MhCGJ8NNazktNNUQNHS4WUpa7hR-rL-play5R0sH4fdVjo)

## Structure of an AST
Okay, this might look like a lot (and it is) but let's look at it's structure before panicking.The boxes are called `nodes`. Each `node` may have a number of `children`. The directional arrows indicate that
the node on the pointy end of the arrow is a child of the node at the flat end.

## But what does it mean?
Now, lets start from the top. We see a `Binary` node, this has no relation to the binary working in you computer, it refers 
to the number of operands. The addition operation `+` adds a left hand number (in this case 1) to a right hand number.
But we don't have a number on the right hand side we have another `Binary`. That is okay, remember at this point 
we are not doing any math just giving the input string more meaning for the computer. As a human, 
its trivial to tell that the `(2 * 4)` should be done before the `1 +` but the computer is not that smart.
The genius of the **AST** is that we can convey this "do a before b" thinking to the computer.
When the computer eventually goes to evaluate the tree the logic is pretty simple

```cs
int eval(Node n) {
    if (n is Binary b) {
        var lhs = eval(b.lhs)
        var rhs = eval(b.rhs)
        if(b.operator == Plus) {
            return lhs + rhs
        }
        else {
            return lhs * rhs
        }
    }
    else {
        var num = (Number)n
        return num.value
    }
}
```
the recursive nature of `eval` is what gives us operator precedence. 
The **AST** also does not have any of the parentheses, spaces or anything that is not strictly related to the operations
the computer needs to do to evaluate this expression. The parentheses meaning of "do this first" is encoded in the 
structure of the tree.

## Actually making the tree
Taking the input expression(s) and turning them into an **AST** is the job of a _parser_ and _tokenizer_.

### Tokenizer
This has not been mentioned until now, but the tokenizer is a function that runs before the parser it (somewhat unsurprisingly)
turns the input data into a stream of tokens. Tokens are a small pieces of a language that can be thought of as "atomic"
in the expression above a token stream would look like:
`NUMBER(1), PLUS, LPAREN, NUMBER(2), STAR, NUMBER(4), RPAREN`
Now, for making the expression atomic is seems to have gotten...longer? That's because the tokens are "atomic" for the computer
not necessarily us. At this point, we no longer have to think about strings we can think about tokens! Oh, and all the spaces are gone.
Actually our tokenizer has the superpower of stripping away spaces (anyone who has worked with human text before knows how valuable this is).
Which means that `1          + (2*          4)` is gets tokenized as exactly 

`NUMBER(1), PLUS, LPAREN, NUMBER(2), STAR, NUMBER(4), RPAREN` just as `1 + (2 * 4)` got tokenized to the same thing.

### Parser
Okay, we have our token stream in hand...what do we do with it? We hand that stream to the parser. The parser is a function
which takes our token stream as input and produces an **AST** as output. There are quite a few methods to parse, so many in fact
I think each one should get it's own page. for a good overview I can recommend [this](https://medium.com/@chetcorcos/introduction-to-parsers-644d1b5d7f3d) 
blog post by Chet Corcos.

- My post on [LR & LALR](@/lalr-generator.md)
- [LL](https://en.wikipedia.org/wiki/LL_parser)
- [Recursive Decent](https://www.booleanworld.com/building-recursive-descent-parsers-definitive-guide/)

(This is not a complete list, just common parsers I have run into.)