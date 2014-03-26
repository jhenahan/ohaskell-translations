### Higher-order functions ###

Higher-order functions are very important in Haskell. From previous chapters you have learnt that pure functions are in fact values. Therefore you can, first of all, pass pure functions as arguments to other functions, and, second, return them from other functions.

A higher-order function is a function, which takes another function as an argument and/or returns another function.

#### Exposing functions ####

As you remember, in the chapter about pure functions I've mentioned, that they can take one or more arguments. It's time to say that I deceived you, as the truth is:

>**Pure functions in Haskell always take one argument**

But how could we declare functions taking two or event three arguments?

The trick is so called "currying". This name comes from Haskell Curry. Currying is a transformation of function taking multiple arguments to a function taking them one by one.

Let's define a function to divide two numbers:

```haskell
divide :: Double -> Double -> Double
divide arg1 arg2 = arg1 / arg2
```

This function takes two values of standart type `Double` and returns the result of dividing the first argument by the second. That is simple. But if we look inside this function call:

```haskell
main = print (divide 10.03 2.1)
```

then we'll know, that this call occurs in two steps:

* Function `divide` is applied to the first argument `10.03` and - attention! - returns the function of the type `Double -> Double`.
* This returned function, in turn, is applied to the second argument `2.1` and returns the final value `4.77`.

We can explicitly state this two steps by rewriting the function call:

```haskell
(divide 10.03) 2.1
```

The function is applied only to one value: first to `10.03`, next the function returned by the first call is applied to `2.1`.

It is because of these two steps the `divide` definition contains two arrows instead of one:

```haskell
divide :: Double -> Double -> Double
```

From the conceptual point of view this declaration sounds like: "The function `divide` takes two values of type `Double` and returns a value of type `Double`." But the more correct way to read this will be: "The function `divide` is applied to the first `Double` value and returns a function of type `Double -> Double`, which is applied to the second `Double` value and returns the final value of type `Double`."

The correct way of reading can be shown in the declaration:

```haskell
divide :: Double -> (Double -> Double)
```

Now we clearly see, that on the first step we have a call of the function of one argument, which returns a function of the type `Double -> Double`, and on the second step we have a call of the second function, returned on step one.

Similarly, if we have a function which takes three arguments:

```haskell
totalSum :: Double -> Double -> Double -> Double
totalSum arg1 arg2 arg3 = arg1 + arg2 + arg3
```

then its call:

```haskell
main = print (totalSum 10.03 2.1 45.7)
```

would occur in three steps and, to reflect this, we can rewrite this function's declaration as:

```haskell
totalSum :: Double -> (Double -> (Double -> Double))
```

and its call as:

```haskell
((totalSum 10.03) 2.1) 45.7
```

And, to clarify things completely, let's study one important thing.

#### Partial function application ####

In spite of the two-step nature of `divide` function call, its body will be executed only once. A single call, just divided into two steps. And to understand the nature of these steps, let's study ***partial application*** of functions.

The function is called partially applied, if the number of arguments, to which it is applied, is less than the number expected by this function. Already familiar λ-functions will be useful here.

Let's apply the function `divide` to just a single argument:

```haskell
main =
    let temporaryFunction = divide 10.03  -- remember first value
    in
    print (temporaryFunction 2.1)         -- now we can complete the job
```

Now everything is in its place. We can clearly see, what does this expression mean:

```haskell
(divide 10.03) 2.1
```

We can't get the result of the division as a result of the first call, because we don't have the second argument yet! Instead we got a temporary λ-function, which we associated with the expression `temporaryFunction` just for clarity. This temporary λ-function kind of remembered the first argument, and only when we apply it to the second argument, we will get the result of the division.

Similarly, the `totalSum` function call, which occurs in three steps, can be described as:

```haskell
let firstFunction = totalSum 1.0        -- Remember the first
    secondFunction = firstFunction 2.0  -- Remember the second
in
print (secondFunction 3.0)              -- Now perform the addition
```

During the call we got two temporary λ-functions, each of which was applied to the next argument and remembered it. Only when the second temporary function λ-function was applied to the third argument, we got the sum.

#### Why do we need it ####

In most cases you don't need this information about currying and partial application. The main advantage of the approach, when one function of multiple arguments is decomposed into a chain of functions of one argument, lies in the academical plane: it is easier to do mathematical proofs, if you agree that every function takes strictly one argument and returns strictly one value.

But we, as programmers-practitioners, are more interested in the practical aspect. That's why we return to considering higher-order functions (HOFs).

Formally `divide` and `totalSum` are HOFs because of those intermediate λ-functions. In fact all functions that take more than one argument are HOFs. But all these intermediate λ-functions are nothing more than internal business, they are hidden from us. The "real" HOFs, which are declared as taking functional values or returning functional values, are more interesting.

Consider a small example:

```haskell
type Login = String
type Password = String
type AvatarURL = String
type UserId = Integer

userInfo :: Login -> Password -> AvatarURL -> UserId -> String
userInfo login password avatarURL userId =
    "Full info about user @" ++ (show userId) ++ ":"  ++
    "\n login: " ++ login ++
    "\n password: " ++ password ++
    "\n avatar URL: " ++ avatarURL

type EmptyInfo = Login -> Password -> AvatarURL -> UserId -> String
type WithLogin =          Password -> AvatarURL -> UserId -> String
type AndWithPassword =                AvatarURL -> UserId -> String
type AndWithAvatarURL =                            UserId -> String

storeLoginIn :: EmptyInfo -> UserId -> WithLogin
storeLoginIn emptyInfo userId =
    emptyInfo "denis"
    {- in real project we should get the login
       according to the passed userId -}

storePasswordIn :: WithLogin -> UserId -> AndWithPassword
storePasswordIn infoWithLogin userId =
    infoWithLogin "123456789abc"
    {- in real project we should get the password
    	according to the passed userId -}

storeAvatarURLIn :: AndWithPassword -> UserId -> AndWithAvatarURL
storeAvatarURLIn infoWithPassword userId =
    infoWithPassword "http://dshevchenko.biz/denis_avatar.png"
    {- in real project we should get the URL
    	according to the passed userId -}

main =
    let userId = 1234
        infoWithLogin = storeLoginIn userInfo userId
        infoWithPassword = storePasswordIn infoWithLogin userId
        infoWithAvatarURL = storeAvatarURLIn infoWithPassword userId
        fullInfoAboutUser = infoWithAvatarURL userId
    in
    putStrLn fullInfoAboutUser
```

Now let's deconstruct this into pieces.

First of all, a new construction appeared:

```haskell
type Login = String
```

The keyword `type` adds an alias for an already known type. Now we can use `Login` instead of `String`.

Next, we declared a function:

```haskell
userInfo :: Login -> Password -> AvatarURL -> UserId -> String
```

This is simple: the function `userInfo` expects a login, a password, an avatar URL and a user ID and returns a string with description. Pay attention to the double plus sign:

```haskell
"\n login: " ++ login
```

This is a string concatenation operator.

Now the interesting stuff begins. We assume that we only have a user ID and we need to get the corresponding login, password and avatar URL from somewhere. Fortunately we have three functions, each of which knows, where to find login, password and avatar URL correspondingly. And each of them is an HOF.

Let's consider aliases:

```haskell
type EmptyInfo = Login -> Password -> AvatarURL -> UserId -> String
type WithLogin =          Password -> AvatarURL -> UserId -> String
type AndWithPassword =                AvatarURL -> UserId -> String
type AndWithAvatarURL =                            UserId -> String
```

Each of them introduces a simple name for a functional type, which is formed by cutting the type of `userInfo` function. Note, that the next type expects one argument less that the previous one. These aliases name types for each intermediate λ-function, which are, as you have already guessed, used for partial application of `userInfo` function.

Consider the first call:

```haskell
infoWithLogin = storeLoginIn userInfo userId
```

Here we pass `userInfo` function as a first argument of function `storeLoginIn`, inside which we apply passed function `userInfo` to the only argument, namely login. Respectively, at `storeLoginIn` function exit we receive the first intermediate λ-function, in which we saved the login value (that's why this λ-function type is associated with the word `WithLogin`).

Next is the call:

```haskell
infoWithPassword = storePasswordIn infoWithLogin userId
```

Here we pass our intermediate λ-function as the first argument of the function `storePasswordIn`. This function, in turn, applies the λ-function passed to it to the only argument, namely the password. So at `storePasswordIn` function exit we have the second intermediate λ-function, in which two values are saved: login, received during the previous call and the password from this call.

The same is true for the next call:

```haskell
infoWithAvatarURL = storeAvatarURLIn infoWithPassword userId
```

At `storeAvatarURLIn` function exit we receive the third λ-function, in which three values are saved: login, password and path to the avatar.

As a result we apply the third λ-function to the last necessary argument, namely the user identifier:

```haskell
fullInfoAboutUser = infoWithAvatarURL userId
```

Here occurs the "full" call of function `userInfo`,  as a result of which we receive the string with description:

```
Full info about user@1234:
 login: denis
 password: 123456789abc
 avatar URL: http://dshevchenko.biz/denis_avatar.png
```

Thus the function `userInfo` was partially applied three times, receiving one argument each time, and it received all necessary arguments during the fourth call. This can be compared to conveyor belt, at every step of which the function received the next argument.

But why do we need such complications? We can simply pass just `userId` to each of these functions and return not the intermediate λ-function, but directly login, password and path to the avatar correspondingly. For example, instead of `storeLoginIn` function we could define `obtainLogin` function like this:

```haskell
obtainLogin :: UserId -> Login
obtainLogin userId =
    -- obtain login from somewhere and return it
```

But what if we don't want to return login explicitly? Because in case of the partial application we pack the login into the intermediate λ-function (so in fact we hide the login inside it), and in this case we return it and show it to everyone. The first solution might be more suitable.

Or the other example:

```haskell
type UserId = Integer
type Prefix = String

obtainLogin :: UserId -> (Prefix -> String)
obtainLogin userId =
    loginStorage "denis"   -- we assume that the login is somehow obtained
        where loginStorage login prefix = prefix ++ ": " ++ login

main =
    let userId = 1234
    in
    putStrLn ((obtainLogin userId) "My login")
```

Let's learn more about `obtainLogin`:

```haskell
obtainLogin :: UserId -> (Prefix -> String)
obtainLogin userId =
    loginStorage "denis"
        where loginStorage login prefix = prefix ++ ": " ++ login
```

Here we obtain the login from somewhere based on the passed user identificator and hide it inside the λ-function created here by us. As a result `obtainLogin` returns partially applied function, which we apply to the prefix string - and in the end we get the final result.

```
My login: denis
```

You might be interested why have I said about λ-functions? Because we don't have a familiar backslash here:

```haskell
where loginStorage login prefix = prefix ++ ": " ++ login
```

But it doesn't matter, as this notation is identical to the λ-form:

```haskell
where loginStorage = \login prefix -> prefix ++ ": " ++ login
```

Remember I said earlier that it is better to define a trivial function without declaration? This is the exact same case.

It's done. Now you know about higher-order functions. It's because of functions in Haskell can be used as values, we can make flexible combinations from those.
