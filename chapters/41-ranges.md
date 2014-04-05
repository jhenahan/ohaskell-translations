### Ranges ###

Range is a construction which automatically creates a list based on a specified 
attribute.

#### The point ####

If we want to create a list of integers from 1 to 10, we may do it this way:

	[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

alternatively, we can just specify a range:

	[1..10]

Done.

Of course, such trick may be done not just with numbers. For example, that's how
we may get a list of all lowercase letters of English alphabet:

```haskell
main = print ['a'..'z']
```

On the output we'll get a beautiful list of symbols, or simple string.

> "abcdefghijklmnopqrstuvwxyz"

#### Smart ranges ####

Ranges can be specified quite flexibly. That's how we may get a list of all even
numbers from 2 to 30:

```haskell
main = print [2,4..30]
```

We specified a step between the value of elements, other values was created 
automatically.

It is also possible to make a descending list:

```haskell
main = print [120,110..10]
```

On the output we get a list of tens:

> [120,110,100,90,80,70,60,50,40,30,20,10]

However, the compiler wouldn't tolerate the explicit guidance. Therefore, don't
write it this way:

```haskell
main = print [2,4,6..30]
```

Complier will get offended, as you are trying to explain it the thing that it 
perfectly understand itself.

#### Infinite ###

As you remember, the laziness of Haskell lets us operate on infinite lists.
We can create such a list with a range.

For example such a range:

	[1..]

creates an infinite list of integers starting from 1. However, as you already
know that list is actually not infinite, but just long **enough**:

```haskell
main = print $ take 5 [1..]
```

Output:

> [1,2,3,4,5]

We also may specify a step:

```haskell
main = print $ take 5 [2,4..]
```

This gives us:

> [2,4,6,8,10]

Here they are, the ranges.
