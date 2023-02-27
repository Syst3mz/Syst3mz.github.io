+++
title = "Nimbus"
date = 2022-02-01
weight = 1
+++

Nimbus is an interpreted multi-paradigm general purpose programming language that I am developing.

Nimbus is designed to be the fastest programming language to write code in, and will make sacrifices in all other areas 
to meet that goal. I am writing it to increase my productivity as a programmer and as a learning experience.

## Planned Features

### Familiar Syntax
```nimbus
fn fibbo(Int:n) -> {
    if n == 1 || n == 2 {
        return 1
    }

    return fibbo(n-1) + fibbo(n-2)
}
```
### Semi-Colons only where needed
Note in the above code sample, there are no semicolons. Code blocks, and other "self-ending" language constructs can be 
semicolon free

### Optional and Result Type Syntax
any type can be made optional with the addition of a `?`. For example:
```nimbus
int: alwaysValue = 2 // There is always a non-none value in alwaysValue
int?: maybe = 2 // An option type with a value
int?: no = None // An option type with no value
```
The same syntax can be used to check if a value exists
```
maybe = maybe? + 2
```
If there is no value in `maybe`, if the outer function can return an option then `None` is immediately returned, otherwise 
a `ExpectedSome` exception is thrown.


### Garbage Collection
Supporting the goal of "fast-to-write" automatic memory management is a must. Using garbage collection the programmer is 
free to work on their problem, not fighting a borrow checker or keeping track of cycles.

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

### Tagged & Generic Enums + Pattern Matching
Ripped straight from [Rust](https://www.rust-lang.org/), they allow much more expressive and correct code.

### Gradual Correctness
The Nimbus interpreter will have multiple modes, each more strict than the last. Allow the computer to the hard work of 
finding bugs, free up the human to fix them. Further, the programmer may select the level of safety they want. If they want to
hack something together real quick, throw exceptions go wild. If they want to make code that never crashes? go ahead! The compiler will
support you all the way through


## Goals

### Co-pilot Interpreter
For the co-pilot, the interpreter must be able to run in an "interactive" mode, where a file is watched and AST is generated in realtime for reporting to the IDE. It should provide fixes in real time as errors occur. The Interpreter should assume what the developer is *wrong* because as they are writing it, their code is almost certainly wrong.

# Progress
<details>
<summary>
    Work in progress grammar for Nimbus
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

## Instantiation modifier
the `abstract` keyword can be used to prohibit instantiation of a class.
```ebnf
abstract       -> "abstract"
```

## Functions
Functions and anonymous functions.
Overloading is done with `A.SomeFunc()` where `A` is the super class.
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