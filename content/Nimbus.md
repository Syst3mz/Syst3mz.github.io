+++
title = "Nimbus"
date = 2023-01-16
+++

Nimbus is an interpreted multi-paradigm general purpose programming language that I am developing.

Nimbus is designed to be the fastest programming language to write code in, and will make sacrifices in all other areas 
to meet that goal. I am writing it to increase my productivity as a programmer and as a learning experience.

## Planned Features

### Familier Syntax
```nimbus
fn fibbo(Int:n) -> {
    if n == 1 || n == 2 {
        return 1
    }

    return fibbo(n-1) + fibbo(n-2)
}
```
### Semi-Colons only where needed
Note in the above code sample, there are no semi colons.

### Optional and Result Type Syntax
any type can be made optional with the addition of a `?`. For example:
```nimbus
int: value = 2 // There is value in value
int?: maybe = 2 // An option type with a value
int?: no = None // An option type with no value
```
The same syntax can be used to check if a value exists
```
maybe = maybe? + 2
```

### Garbage Collection
Using a well understood Mark-Sweep approach to garbage collection, I can reduce mental overhead when solving problems.

### Multiple Inheritance
While problems like the well studied [diamond problem](https://en.wikipedia.org/wiki/Multiple_inheritance#:~:text=The%20%22diamond%20problem%22%20(sometimes,from%20both%20B%20and%20C. )
exist, it is my opinion that the increase of re-usability in code that multiple inheritance can provide is well worth it.

### Function Level Structural Subtyping (Restricted Duck-Typing)
While multiple inheritance can cover a lot, I feel that allowing functions to explicitly define what they need to work 
will allow a massive increase in re-usability.
```nimbus
// syntax under heavy development!!!
fn add_one<T: T.Int:number>(T:holder) {
    holder.number += 1
}
```

### Tagged & Generic Enums
Ripped straight from [Rust](https://www.rust-lang.org/), they allow much more expressive and correct code.

### Gradual Correctness
The Nimbus interpreter will have multiple modes, each more strict than the last. Allow the computer to the hard work of 
finding bugs, free up the human to fix them. 


## Goals

### Co-pilot Interpreter
For the co-pilot, the interpreter must be able to run in a "interactive" mode, where a file is watched and AST is generated in realtime for reporting to the IDE. It should provide fixes in real time as errors occur. The Interpreter shuold assume what the developer is *wrong* because as they are writing it, their code is almost certainly wrong.


# Stages
To accomplish the herculean task of writing my own programming language, I have split up the challenges into the 
following phases (loosely following the architecture of a compiler).
- Lexical analysis
- Parsing
- Reasonable type inference
- Optimization
- Interpretation

# Progress
Well, its a work in... Right now I am working on making a [Parser Generator](@/lalr-generator.md) to support Nimbus.

