### Data immutability ###

One of the fundamental properties of Haskell is an absence of an assignment operator.

When I first heard about this property, I couldn't believe it. How is it possible to code without an assignment operator? And how will we change the state of our variables? My surprise was understandable: when writing C++ code I often use the assignment operator.

To understand, consider the following expression:

    a = 123

This construction means assignment in imperative languages. In this case, we say: "Take the sequence of bytes, corresponding to the value `123`, and change the sequence of bytes, stored in the variable `a`, with it." Thus, the overwriting of the old value with the new value occurs.

But in pure functional language this instruction means the same that it does in maths, namely the equality. In this case we declare: "The value of `a` is equal to `123`."

What's the difference, you may ask. In any case we get a variable `a` with the value `123`. The difference is that the assignment of the same variable can occur many times, but at the same time the equality declaration can occur only once. So if we declared, that the value of `a` is `123`, then it will be so forever. That's why Haskell has neither the concept of "variable", nor the `const` keyword, because all values are constant in it.

You might be interested how to add an element to a collection if everything is constant? The answer is: you can't do it. We can't change the value, we can only create a ***new*** value from it. Do not worry about the memory: it will be allocated and freed automatically.

You'll soon see that you can easily live without the assigment operator.
