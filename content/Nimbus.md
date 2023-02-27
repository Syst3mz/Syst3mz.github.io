+++
title = "Nimbus"
date = 2022-02-01
weight = 1
+++

Nimbus is an interpreted multi-paradigm general purpose programming language that I am developing.

I am designing Nimbus to be the fastest programming language to write code in. To meet that goal Nimbus will make sacrifices to its safety and run-time speed. I am writing Nimbus, not only as a learning experience, but to increase my productivity as a programmer.

## Planned Features

### Familiar Syntax
```nimbus
fn fibbo(Int:n) -> Int {
    if n == 1 || n == 2 {
        return 1
    }

    return fibbo(n-1) + fibbo(n-2)
}
```
### Semicolons Only Where Needed
Note in the above code sample, there are no semicolons. Code blocks and other "self-ending" language constructs can be 
semicolon free.

### Optional and Result Type Syntax
Any type can be made optional with the addition of a `?`. For example:
```nimbus
int: alwaysValue = 2 // There is always a non-none value in alwaysValue
int?: maybe = 2 // An option type with a value
int?: no = None // An option type with no value
```
The same syntax can be used to check if a value exists.
```
maybe = maybe? + 2
```
If there is no value in `maybe` and if the outer function can return an option, then `None` is immediately returned; otherwise 
a `ExpectedSome` exception is thrown.


### Garbage Collection
**A**utomatic **M**emory **M**anagement (AMM) is a must to support the goal of "fast-to-write." Nimbus will use a garbage collector to handle AMM. Using a garbage collector lets the programmer focus on the program, not on complex move semantics or preventing memory cycles.

### Multiple Inheritance
Whereas problems like the well studied [diamond problem](https://en.wikipedia.org/wiki/Multiple_inheritance#:~:text=The%20%22diamond%20problem%22%20(sometimes,from%20both%20B%20and%20C. )
exist, the increase of re-usability in code that multiple inheritance can provide is well worth the challenges associated with multiple inheritance.

### Function Level Structural Subtyping (Restricted Duck-Typing)
To supplement multiple inheritance, allowing functions to explicitly define what data they need to work 
will allow a massive increase in re-usability.
```nimbus
// syntax under heavy development!!!
fn add_one<T: T.Int:number>(T:holder) {
    holder.number += 1
}
```

### Tagged & Generic Enums + Pattern Matching
Nimbus will rip [Rust's](https://www.rust-lang.org/) excellent enums and pattern matching because they allow expressive and correct code.

### Gradual Correctness
The Nimbus compiler will have multiple levels of safety with safer code requiring stricter compile-time checks. The programmer may chose to hack something together quickly using exceptions to handle errors. Later, they can come back switch a compiler flag, and the Nimbus compiler will enforce safety.

### Co-pilot Compiler
For the co-pilot, the compiler must be able to run in an "interactive" mode, where a file is watched and an AST is generated in real time for reporting to the IDE. It will provide fixes in real time as errors occur. The compiler should assume that the developer is *wrong* because as they are writing it, their code is almost certainly wrong. 

# Progress
<details>
<summary>
    Nimbus's grammar (work in progress)
</summary>

```ebnf
program        -> declaration+
```

## Declarations
```ebnf
declaration    -> abstract? classDecl
				| fnDecl
				| varDecl
				| enumDecl
				| useDecl

classDecl      -> "class" identifier ("<" identifierl ">")? inheritl? "{" classitem* "}"
inherit        -> ":" (gtype | stype)
classitem      -> amod classDecl
                | amod fnDecl
                | amod varDecl
                | useDecl
                

funDecl        -> fn

varDecl        -> "imut"? "var" (type ":" )? identifier ("=" expression)?

useDecl        -> "use" namespace ("as" identifier)?
namespace      -> (identifier ".")* identifier

enumDecl       -> "enum" identifier ("<" identifierl ">")? "{" varientl "}"
variant        -> identifier ("(" typel ")")
```

## Statements
```ebnf
statement      -> exprStmt
                | forStmt
                | ifStmt
                | returnStmt
                | whileStmt
                | block

exprStmt       -> expression ";"
forStmt        -> "for" identifier "in" expression block
ifStmt         -> if
returnStmt     -> "return" expression?
whileStmt      -> "while" expression block
block          -> "{" (varDecl | statment)* expression?"}"
```

## Expressions
```ebnf
expression     -> assignment
                | if
                
if             -> "if" expression block else?
else           -> ("else" "if" expression block)* "else" block

assignment     -> ( call "." )? identifier "=" assignment
               | logic_or

logic_or       -> logic_and ( "||" logic_and )*
logic_and      -> equality ( "&&" equality )*
equality       -> comparison ( ( "!=" | "==" ) comparison )*
comparison     -> term ( ( ">" | ">=" | "<" | "<=" ) term )*
term           -> factor ( ( "-" | "+" ) factor )*
factor         -> unary ( ( "/" | "*" ) unary )*

unary          -> ( "!" | "-" ) unary | call 
call           -> primary ( "(" exprl ")" | "." identifier )* 
primary        -> "true" 
                | "false" 
                | "none" 
                | "this"
                | number 
                | string 
                | char
                | identifier 
                | "(" expression ")"
                | "super" "." identifier 
```

## Access
Access modifiers are used to prohibit certain parts of code from being seen outside the "inner" scope of the instantiater. They may be applied to classes, enums, and functions.

| Access Mode | Short Name | Effect                                                        |
|-------------|------------|---------------------------------------------------------------|
| Public      | pub        | Any outer scope can see the declaration                       |
| Protected   | prot       | Only outer scopes which are a subtype can see the declaration |
| Private     | priv       | No outer scopes can see the declaration                       |

```ebnf
amod           -> pub
                | prot
                | priv

pub            -> "pub" | "public"
prot           -> "prot" | "protected"
priv           -> "priv" | "private"
```

## Instantiation Modifier
the `abstract` keyword can be used to prohibit instantiation of a class
```ebnf
abstract       -> "abstract"
```

## Functions
Functions and anonymous functions;
overloading is done with `A.SomeFunc()` where `A` is the super class.
```ebnf
anonFunc       -> "(" identifierl ")" "->" block
fn             -> "fn" (identifier ".")? identifier ("<" identifierl ">")? "(" fnDeclArgl ")" "->" block
fnDeclArg          -> type ":" identifier
```

## Types Literals
```ebnf
stype          -> identifier
atype          -> type "[]"
gtype          -> identifier "<" identfierl ">"
otype          -> type "?"
type           -> stype 
                | atype 
                | gtype
                | otype
```

## Utilities / Lists
```ebnf
typel           -> (type ",")* type ","? 

identfierl      -> (identifier ",")* type ","?

namespacel      -> (namespace ",")* namespace ","?

fnDeclArgl      -> (fnArg ",")* fnArg ","?

variantl        -> (varriant ",")* variant ","?

exprl           -> (expression ",")* expression ","?

inheritl        -> (inherit ",")* inherit ","?
```

</details>

The parser is work in progress, and you can check on the progress at [LALR parser generator](@/lalr-generator.md).