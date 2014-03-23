### Pure functions ###

Since Haskell is a purely functional language, let's discuss pure functions
as one of the cornerstones of the language.

To begin with, let's recall some school math and formulate the basic definition
of a function.
	
>**Function is a description of a dependency of something from something**

Pure functions in Haskell are functions in mathematical sense themselves.
They are descriptions of how the input value define the output value.

Hence follows an important feature of pure functions, precisely the lack of
*side-effects*. Input value of a pure function entirely and completely defines
its output value. Therefore if we pass same value as input million times, we are
guaranteed that on the output we'll get the same result million times.

#### Declaring ####

Same as in other programming languages, function should first be declared. Let 
us do that.

```haskell
simpleSum :: Int -> Int
```

Before the symbol `::` we declared the name of the function, then follows its
type.

	simpleSum :: Int -> Int
	|       |    |        |
	  name          type

I understand that the collocation "type of function" sounds weird, but pure 
function is a value that has type of function.

Consider the declaration of this type:

```haskell
Int -> Int
```

Notice an arrow. This arrow itself means that we face the pure function. The type
of the only argument (in this case it's the standard type `Int`) is on the left
side of arrow, on the right side we have an output type (same `Int`). The arrow
may be considered as a "mental designation" to the stream of information which
pass through the function from input to output, left to right.

I should remind you that pure function must have at least one argument and return
something, cause that is the point of mathematical function.

Concerning amount of arguments. Of course function may take several arguments. 
Here is a type of function that takes three arguments.

```haskell
Int -> Int -> Int -> Int
```

That notation should be read as follows: find the last (most right) arrow, that's
the separator: to the left from it goes the list of arguments' types, to the right
type of the output.

	Int -> Int -> Int -> Int
	arguments' types |  | output's type

#### Defining ####

Now the function needs to be defined. By the way, the function *must* be defined.
For example in the `C` or `C++` language we may declare the function, but not
define it (provided that it is never called) and stay calm. Haskell has a stricter
approach: if you declared the function, please be so kind to define it, 
otherwise the compiler will express it's categorical discontent.

That's why right after the declaration we write the definition:

```haskell
simpleSum :: Int -> Int
simpleSum value = value + value
```

In this case the "mental separator" is the equal sign. Skeleton of that expression
may be expressed this way:

	NAME ARGUMENTS = BODY_EXPRESSION

where `NAME` is function's name, `ARGUMENTS` is the list of arguments (their names,
not types), and `BODY\_EXPRESSION` is the body of function. In that case we have
the only argument namely `value`, we also have a simple body where we just sum
argument with itself.

#### Calling ####

Now our function may be called. Let us do that with argument 4, or as we say
in world of `FP`, apply the function to the argument 4:

```haskell
main = putStrLn (show (simpleSum 4))
```

Result:

	8

Done. Now I should clarify some important details.

#### Exit from function ####

In the `C` language, if you have a function with returnable type, we must
specify the exit points using `return` instruction. Moreover, there may be
several exit points.

It's very different with Haskell. Firstly, there may be only one exit point.
Secondly, Haskell has no analog for `return` instruction. If we recall the
mathematical nature of a pure function, we'll understand that there is no another way.
After all, the pure function is a description of the dependency of the output
value from the values on input, that's why it's body is an aggregate of 
expressions which are evaluated and in result they give only one, the last 
expression. That last expression is actually the "exit point" of the function.

I'll give an example:

```haskell
indicate :: String -> String
indicate address = 
    if adress == "127.0.0.1" then "localhost" else address
```

This function takes an argument of standard type `String`, which represents some
IP-address. In the function's body argument is checked on equality to `127.0.0.1`,
in result we end up in one out of two logical branches. In `C++` it will look
like that.
	
```c++
std::string indicate( const std::string& address ) {
    if( address == "127.0.0.1" ) {
        return "localhost";
    }
    return address;
}
```

We explicitly pointed two exit points. But in Haskell we don't need to do that,
as when we will get into one of the branches the selected expressions would be
returned.

To make it more clear, let's refactor the body of this function and get rid of
`if-then-else` expression:

```haskell
indicate :: String -> String
indicate "127.0.0.1" = "localhost"
indicate address = address
```

Haskell allows to set several definitions for the same function. Consider it as
a special type of overload. Here we say: "If the input argument equals to
`127.0.0.1` use the body №1, otherwise use the body №2." Therefore, when compiler
will see call to that function in code it will just substitute it with the 
corresponding expression: either string `localhost` in case if the first body
is used, or the passed argument in case if the second body is used.

Now everything fell into place: there is no need to explicitly specify the exit
point of pure function, because the final expression in the body of this function
will just substitute the call to it. That is, if it's written this way:

```haskell
main = putStrLn (indicate "127.0.0.1")
```

it is the same thing as just:

```haskell
main = putStrLn "localhost"
```

That is an important property of pure functions: we always can safely substitute
calls to them with returned results, and the application will work as it did 
before. That's why it's easy to work with pure function.

#### Guards ####

There is one more method to perform the selection inside the function with no
use of `if-then-else`. It is called guard. Let's refactor our function one more
time:

```haskell
indicate :: String -> String
indicate address
	| address == "127.0.0.1" = "localhost"
	| null address = "empty IP-address"
	| otherwise = address
```

Symbol `'|'` represents the selection, as if we substitute it with the word
"or". It is followed by logical condition and the corresponding final value of 
the function.

```haskell
| address == "127.0.0.1" = "localhost"
| null address           = "empty IP-address"
...
-- логическое условие    = итоговое значение
```

By the way, branch with condition `otherwise` should always take place. In case 
if you miss it, the code would compile, but a treacherous bug will be settled 
down inside your code. Particularly, if you'll write it these way:

```haskell
indicate :: String -> String
indicate adress
	| address == "127.0.0.1" = "localhost"
	| null adress = "empty IP-address"
```

and then apply the function to a non-empty string, which is not "`127.0.0.1`",
you'll get a runtime error:

	Real: src/Main.hs:(23,1)-(25,36): Non-exhaustive patterns in function

Be careful.

#### Local expressions ####

Local expression inside the function's body is a very useful thing, which can
save us from magic numbers and repetitions.

For example, we have such function:

```haskell
prepareLength :: Double -> Double
prepareLength line =
	line * 0.4959
```

Here we prepare the length of some line by multiplying its original length by
given correction coefficient. However, we got a classical magic number,
the implication of it is not clear and it's bad. Adding comments is not the
best solution. Thus, let's add an expression which will clear the point of that
number.

```haskell
prepareLength :: Double -> Double
prepareLength line =
	line * coefficient
	where coefficient = 0.4959
```

Keyword `where` sets an expression, that may be used inside the function's body.
Consider it as a pseudonym: identifier `coefficient` may now be used as analog
of a numeric value `0.4959`.

There may be several local expressions:

```haskell
...
    line * coefficient - correction
    where coefficient = 0.4959
          correction = 0.0012
```

There is one more way to set the local auxiliary expression, particularly with
the help of the keyword `let`. With the example of our last function, it will look
this way:

```haskell
prepareLength :: Double -> Double
prepareLength line =
	let coefficient = 12.4959
	    correction = 0.0012
	in
	line * coefficient - correction
```

The common structure is as follows:

> let `bindings` in `expression`,

where `bindings` are the local expressions and `expression` is the place where
we are going to use these local expressions.

You are wondering what is the difference between `where` and `let`?

Firstly, it's possible to have only one `where` expression and it should be placed
to the end of the function's body, at the same time `let` can appear many times
in any part of function's body.

Secondly, expression set by `where` keyword is visible from any part of the
function, whereas the expression set by keyword `let`, may be "super-local".
For example:

```haskell
...
	let coefficient = 12.4959
	    correction = 0.0012
	in
	line * coefficient - correction - (let s = 10.9 in s + 1) - s
```

Here we set "super-local" expression with the name `s` which exists only inside
the round parenthesis. That's why that code wouldn't pass the compilation, cause
the second expression `s` is is outside the parenthesis.

#### Without declaration ####

As you remember, we can't declare the function without defining it.
But what about to define the function without declaring it? The answer is: we can.

The common practice is to declare the function and then define it. Yes, we *may*
write it this way:

```haskell
-- There is no declarition only the definition
prepareLength line =
	...
```

however for complicated functions such practice is not recommended, because the
definition becomes poorer, as the description of arguments and type of output
helps to understand the work done by function. Moreover, if you wouldn't specify
these types, they'll become polymorphic, but let's talk about that later.

However, if we consider really trivial functions, consisting of one-two lines,
talking for themselves, then the declaring types of output and arguments will
look excessive. In that case omit them.

That's it, now you know about the pure functions. By the way, they will make you
a lot of use, we'll come back to them in the subsequent chapters.
