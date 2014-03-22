### Three foundations of typing ###

Haskell has a serious relationship with types. His type system is based on three foundations:

1. static checking
2. strictness
3. automatic inference

#### Foundation one ####

Static type checking is a checking of each expression, which is performed during compilation stage. If the compiler doesn't like something in some expression's type, the compilation will fail.

Respectively, if the compilation of Haskell code succeeded, we can say that everything is fine with types, because we have a second foundation.

#### Foundation two ####

Type strictness is a requirement of a correspondence between something we expect and something we get.

For example we can write the following function in C:

```c
int coefficient() {
	return 12.9;
}
```

This is an example of implicit type conversion. We expect a value of type `int` but in fact we get a value of type `double`. However the compiler will allow this and truncate the fractional part of the return value, because this expression's type will be implicitly converted to `int`.

There is no chance for this code to compile in Haskell, because there is no implicit type conversion there: if we expect an integer - you need to provide exactly an integer.

However, explicit type conversion is also very limited. In C++ we can write something like:

```c++
int main() {
	std::cout << (int)'1' << std::endl;
}
```

We took a value of type `char` and remade it into a value of type `int`. The compiler keeps silence. The consequences of such errors are well known.

In Haskell we can explicitly name the type of some value, but only if the type is associated with the value. Like if it is the number `1`, we can only explicitly name a "numerical" type (like `Integer` or `Double`). Tricks with converting a symbol to an integer, like the one shown above, are not possible in Haskell.

#### Foundation three ####

Automatic type inference is a compiler's ability to understand an expression's type by the expression itself.

For example, in C we need to declare the type explicitly:

```c
double i = 10.34;
```

We do not need this in Haskell. We just write:

```haskell
i = 10.34
```

The compiler will analyze the value `10.34` and will understand, that type of `i` is `Double`. However, as it was already said, we can explicitly name the type of the expression (and sometimes we ***must*** do it). I will show it soon.