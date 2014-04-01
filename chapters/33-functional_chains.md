### Functional chains ###

Often you can say about functions that one is not a warrior. In this chapter we'll look at two convenient ways to organize function cooperation.

#### URL example ####

It is known, that URL must satisfy specific rules. But in real life it is not always true, so we need a way to transform URL to a correct format. This is how it can look like:

```haskell
import Data.Char
import Data.String.Utils

addPrefix :: String -> String
addPrefix url =
    if url `startsWith` prefix then url else prefix ++ url
    where prefix = "http://"
        startsWith url prefix = startswith prefix url

encodeAllSpaces = replace " " "%20"   -- Changes all spaces to %20

makeItLowerCase = map toLower         -- Transform letters to lower case

main =
    putStrLn (addPrefix (encodeAllSpaces (makeItLowerCase url)))
    where url = "www.SITE.com/test me/Start page"
```

The output will be:

    http://www.site.com/test%20me/start%20page

We have imported a new module `Data.String.Utils`. This module is a part of `MissingH` package, which contains a lot of useful utilities. Let's install it:

    $ cabal install MissingH

Don't forget to mention it in `Real.cabal`

    build-depends: base ==4.6.*, MissingH

`Data.String.Utils` module has a lot of great things to work with strings, but we needed only one function `startswith`, which checks if one string is the beginning of another.

You might have noticed two unusual strings:

```haskell
encodeAllSpaces = replace " " "%20"

makeItLowerCase = map toLower
```

What is it? It looks like function without a declaration, but where is an argument? We don't need one. To clarify, let's write this with an argument:

```haskell
makeItLowerCase url = map toLower url
```

This is the complete form. But we can shorten it if we remove the argument. Why? Because this form:

```haskell
makeItLowerCase = map toLower
```

says that `makeItLowerCase` is an alias for a functional notation `map toLower`. So from now on, when we write `makeItLowerCase arg`, we assume `map toLower arg`.

Now we have three functions, each of which makes a relatively simple operation with the URL: `makeItLowerCase` converts all symbols to lower case, `encodeAllSpaces` changes spaces to `%20` string, `addPrefix` adds the prefix if it is absent. So in the end we have a chain of three functions: the input is the incorrect URL and the output is the fixed URL. Let's take a closer look on this chain:

```haskell
addPrefix (encodeAllSpaces (makeItLowerCase url))
```

At first `makeItLowerCase` is called, then `encodeAllSpaces`, and then `addPrefix`. Each function receives URL as input and outputs a refined URL, which is the input of the next function.

Everything could be fine, but there is a drawback in this chain - there are too many parentheses. And if we add more correction functions, we'll have even more. There are two ways to make these chains prettier.

#### Function composition ####

Function composition looks like a dot. Its purpose is to join functions into a chain. Like this:

```haskell
(addPrefix . encodeAllSpaces . makeItLowerCase) url
```

Function composition makes one function out of three, which is then applied to `url`, and the result is the same as if we wrote

```haskell
addPrefix (encodeAllSpaces (makeItLowerCase url))
```

You can say that function composition made a stack of three functions: they are written from left to right, but they will be executed from right to left. So `url` string moves through a conveyor from right to left.

#### Application function ####

And there is also an application function or an application operator. It looks like a dollar sign. Its purpose is also to join functions into a chain. Like this:

```haskell
addPrefix $ encodeAllSpaces $ makeItLowerCase url
```

Here we don't have parentheses at all. And this notation is the same as:

```haskell
addPrefix (encodeAllSpaces (makeItLowerCase url))
```

Here we also get a stack of functions: they are written from left to right and are called from right to left. This order is called right-associative.

You're probably interested, what's the difference between these two approaches? Both of them are designed to make chains of functions.

The main difference is that application function allows us to chain not just functions, but a function and its argument:

```haskell
main = print $ "Hi master!"
```

The dollar operator joined the value and the function applied to it. And function composition won't allow you to do that.

Why do we need it, you may ask. I'll answer:

```haskell
main = print ("Hi master '" ++ name ++ "', have a nice day")
```

`print` function expects just one argument, so we need to put parentheses around three strings which are concatenated. It is more convenient to write:

```haskell
main = print $ "Hi master '" ++ name ++ "', have a nice day"
```

We got rid of parentheses by joining the function and its argument into a chain. Because of this, function composition and application function are often used together:

```haskell
addPrefix . encodeAllSpaces . makeItLowerCase $ url
```

The dot joins functions and the dollar sign joins them with the argument.

Now you know about functional chains.
