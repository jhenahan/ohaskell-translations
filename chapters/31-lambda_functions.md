### λ-functions ###

Now we need to get acquainted with a curious and important concept, namely λ-functions (lambda-functions).

Let's recall the definition of the mathematical function from the previous chapter:

>**Function is a description of a dependency of something from something**

However in C (or similar languages) the function was never associated with such definition. Instead, the function there is nothing else than a subprogram, and the name of the function is a pointer to its first instruction.

Besides, function in C is global in the current translation unit. So a function call is kind of a "global `goto`" into its body with a subsequent return from it. That's why the function in C cannot be nameless, otherwise you won't be able to call it.

λ-function is another kind of monster.

#### What is it? ####

The basis of λ-functions is λ-calculus, which is named after a beautiful greek letter. λ-calculus has a long academic history, but we are interested in a practical side, so I'll give an example.

Assume we need a function, which takes an integer value and returns a square of it. We can describe this function as a:

    5 > f > 25

As simple as possible: we have `5` as input, `25` as output. The internals of this function can be described as:

    5 > (x*x) > 25

Now the main question: how do we describe this function in a ***formal*** way? That's where λ-calculus comes to help, because it provide a formal way of writing functions. Our function will be described as:

    λx.x*x

The letter λ is a sign of a λ-function. This expression must be read like: "λ-function of one argument x, which returns a result of multiplying this argument by itself."

The dot is a separator here. The expression to the left is a list of arguments (in this case we have one argument), and the one to the right is a function body.

A simple and elegant description, nothing unnecessary. Not even a name. The feature of a λ-function is the absence of a name, because it doesn't need one. That's the main difference from the "general" function.

#### How does it look like in code ####

λ-functions exist in many languages, but in Haskell their appearance is the closest to mathematical. Compare

    λx.x*x     -- mathematical notation
    \x -> x*x  -- Haskell notation

A direct correspondence. Even the backslash fits perfectly: it can be viewed as a "back" of λ. The only difference is a substitution of a dot with an arrow.

How to call this function, you might ask. The answer might be surprising, but, strictly speaking, λ-functions are not called. But it is just a game of words. Let's head back into maths for a minute.

The idea of λ-function is based on a mathematical principle of "application". λ-functions are not called with an argument, but are applied to an argument. That is why the expression

    f a

is read like: "The application of function `f` to an argument `a`."

This is how it looks in Haskell:

```haskell
(\x -> x * x) 5
```

λ-expression in parenteses gives a λ-function, which is immediately applied to an argument `5`.

#### Multiple arguments ####

λ-function can be applied to multiple arguments. Assume we have a function, which returns the result of a multiplication of the first value to the second:

```haskell
main =
    print (f 5 6)
        where f = \arg1 arg2 -> arg1 * arg2
```

The argument list is between the backslash and the arrow.

#### What's the use ####

In C we have a standart sequence of three steps when working with a function:

1. declaration
2. definition
3. call

For example:

```c
int sq(int i) {
	return i*i;
}

int main() {
	printf("%d", sq(5));
}
```

We prepare our "global subprogram" and then enter it via a call.

And that's how it looks in Haskell:

```haskell
main = print ((\x -> x * x) 5)
```

We prepare nothing in advance. On the contrary, we create a function as a value, locally and immediately before usage. We create it and immediately apply iy to the argument `5`.

For simplicity we can introduce an explanatory expression for our function:

```haskell
print (f 5)
    where f = \x -> x * x
```

The main advantage of a λ-function is in its locality. Why do we need to declare and define the function in advance if we can create and immediately use in exactly where we need it?

Of course if λ-funciton is used in many places, we can define it globally to avoid duplication by binding it with some name. For example:

```haskell
f = \x -> x * x

main = print ((f 5) + (f 6))
```

The expression `f` is equal to our λ-function, and now we can apply it multiple times to different arguments.

Done. Now you now what λ-functions are. But their most interesting application is connected with higher-order functions, about which we'll talk right now.
