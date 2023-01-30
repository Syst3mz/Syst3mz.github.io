+++
title = "Nimbus"
date = 2023-01-16
+++

# Nimbus
Nimbus is an interpreted multi-paradigm general purpose programming language that I am developing.

Nimbus is designed to be the fastest programming language to write code in, and will make sacrifices in all other areas 
to meet that goal. I am writing it to increase my productivity as a programmer and as a learning experience.

## Planned Features

### Garbage Collection
Using a well understood Mark-Sweep approach to garbage collection, I can reduce mental overhead when solving problems.

### Multiple Inheritance
While problems like the well studied [diamond problem](https://en.wikipedia.org/wiki/Multiple_inheritance#:~:text=The%20%22diamond%20problem%22%20(sometimes,from%20both%20B%20and%20C. )
exist, it is my opinion that the increase of re-usability in code that multiple inheritance can provide is well worth it.

### Function Level Structural Subtyping (Duck-Typing)
While multiple inheritance can cover a lot, I feel that allowing functions to explicitly define what they need to work 
will allow a massive increase in re-usability.

### Tagged Enums
Ripped straight from rust, they allow much more expressive and correct code.

### Gradual Correctness
The Nimbus interpreter will have multiple modes, each more strict than the last. Allow the computer to the hard work of 
finding bugs, free up the human to fix them.


## Stages
To accomplish the herculean task of writing my own programming language, I have split up the challenges into the 
following phases (loosely following the architecture of a compiler).
- Lexical analysis
- Parsing
- Reasonable type inference
- Optimization
- Interpretation

## Progress
Well, its a work in... Right now I am working on making a [Parser Generator](@/lalr-generator.md) to support Nimbus.

