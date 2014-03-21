### Pure functionality ###

Haskell is a general-purpose purely functional programming language.

Historically it became so that today the most popular approach to programming 
is *imperative*. With such approach a program is a sequence of instructions,
which should be executed exactly in the specified order. Furthermore, imperative
programming imply the existence of an assignment operator, as a programmer often
changes the state of many variables.

However, there is a completely different approach to programming, namely
*declarative*. With such approach the program is a set of declarations describing 
the result that should be achieved. Functional programming is one of the incarnations 
of declarative approach. Moreover, Haskell has no assignment operator and all 
variables are not variables at all, they are nothing but *constants*.

To confuse you even more I will mention such features of Haskell as:

1. existence of pure functions,
2. separation of pure functions and functions with side-effects,
3. lazy evaluation.

Sounds a bit weird, so let's take a closer look.
