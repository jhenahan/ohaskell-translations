### Lists at a glance ###

In Haskell lists are collections of same-type elements. Let's proceed to study
them.

The first thing you should learn: if you see square brackets the list is nearby.
Here is a list of three integer elements:

```haskell
[1, 2, 3]
```

and that's the empty list:

```haskell
[]
```

Elements of the list can be of any type, including other lists. We can create
even a list of functions, though that fact shouldn't be a great surprise after
previous chapters.

#### The simplest actions ####

If a list can be created it may be useful for someone. Here is a function that
returns a list of three lines:

```haskell
listOfNames :: String -> [String]
listOfNames prefix =
	[prefix ++ "John", prefix ++ "Anna", prefix ++ "Andrew"]

main = print $ listOfNames "Dear "
```

Result:

> ["Dear John","Dear Anna","Dear Andrew"]

Note the declaration of that function

```haskell
listOfNames :: String -> [String]
```

Type `[String]` is a type of list of strings. And for example, list of characters
is declared as `[Char]`. By the way, string is a list of characters itself, hence
type `String` is equivalent to type `[Char]`. That's why we may declare it this
way:

```haskell
listOfNames :: String -> [[Char]]
```

The size of the list may be found this way:

```haskell
main =
    print $ length listOfAnimals
	where listOfAnimals = ["Bear", "Tiger", "Lion", "Wolf"]
```

Also we may check if a given value appears in the list:

```haskell
thisIsAWildAnimal :: String -> Bool
thisIsAWildAnimal name =
    name `elem` wildAnimals
    where wildAnimals = ["Bear", "Tiger", "Lion", "Wolf"]

main = print $ if thisIsAWildAnimal "Cat" then "Yes!" else "No!"
```

The function `elem`, written with [infix](The function is placed between two 
arguments similar to when the binary operator is placed between its operands.) notation,
checks the presence of a string `Cat` in the list of wild animals.

The standard Haskell library let us do various things on the list, such as 
extracting the smallest value, computing the sum of elements, extracting the
sublist, check on the emptiness and equality, etc. 

#### Immutability of the list ####

As you already know, in Haskell all values are immutable as Egyptian pyramids.
Lists are not an exclusion: we can't change the list, we only can create new list
based on the old one. For example:

```haskell
addNewHostToFront :: String -> [String] -> [String]
addNewHostToFront newHost listOfHosts = newHost : listOfHosts

main =
	print $ addNewHostToFront "124.67.54.90" listOfHosts
	where listOfHosts = ["45.67.78.89", "123.45.65.54", "127.0.0.1"]
```

Output:
> ["124.67.54.90","45.67.78.89","123.45.65.54","127.0.0.1"]

Conceptually, function `addNewHostToFront` added a new address to the beginning of
the given list. Actually, there was no addition: function just took the element
`newHost` and list `listOfHosts` and based on them created a new list, which
already contains of four elements instead of three.

#### Actions on elements ####

We create a list in order to do something with its elements. Consider such 
function:

```haskell
removeAllEmptyNamesFrom :: [String] -> [String]
removeAllEmptyNamesFrom listOfNames =
	filter notEmptyName listOfNames
	where notEmptyName = not . null
main =
	print $ removeAllEmptyNamesFrom listOfNames
	where listOfNames = ["John", "", "Ann"]
```

Standard function `filter` successively applies predicate `notEmptyName` to each
line of the list and constructs a new list only from those lines which match to
this predicate. As a predicate we have a function which is applied to one argument
and returns `True` only if it is not `null`. Note that we used pointfree notation
again.

That's it. Besides the functions `map` and `filter`, standard Haskell library has
lot's of other goodies to work with elements (assertions, substitutions, sorts,
permutations and other things).
