### A minimum about modules ###

Real projects never consist of a single file. It's time to learn about modules.

Source files in a Haskell project are modules. One file is one module. So there are two modules in our project now: `Main.hs` and `Helpers.hs`.

Haskell has no header files. Each module is considered as a separate unit, containing different stuff. And in order to use this stuff you need to import a module.

Let's open empty file `Helpers.hs` and write

```haskell
module Helpers where
	
hello user = "Hi, " ++ user
``` 

The first string declares the name of the module - `Helpers`. Then, after `where` keyword, we fill our module with contents. The content we have is really simple, but don't ask me yet, what `hello` is. We will soon find out.

#### Importing ####

Now we open `Main.hs` file and change it a little bit:

```haskell
import Helpers

main = putStrLn (hello "denis")
```

We included the `Helpers` module using `import` statement. Now we can use the contents of the module, namely that thing by the name `hello`.

#### Mentioning ####

Now we need to tell the build file `Real.cabal` about the `Helpers` module. Let's open it and write:

    executable Real
        main-is:        Main.hs
        other-modules:  Helpers
        build-depends:  base ==4.6.*
        hs-source-dirs: src

We have uncommented the string with `other-modules` and mentioned the name of our module there. Pay attention: you need to specify the name of the module, not the filename.

But since we specified the name of the module, we need to specify the place where to look for it. After all, it is not in the `src` folder, but in the `src/Utils` subfolder. So we need to add something to `hs-source-dirs` parameter:

    hs-source-dirs: src
                    src/Utils

Now save and build.

    $ cabal build
    Building Real-0.1.0.0...
    Preprocessing executable 'Real' for Real-0.1.0.0...
    [1 of 2] Compiling Helpers          ( src/Utils/Helpers.hs, dist/build/Real/Real-tmp/Helpers.o )
    [2 of 2] Compiling Main             ( src/Main.hs, dist/build/Real/Real-tmp/Main.o )
    Linking dist/build/Real/Real ...

It worked - not one, but two modules were compiled. Now launch.

    $ ./dist/build/Real/Real
    Hi, denis

And it works.

#### About names ####

There are two rules.

First, the name of the module must start with a capital letter. The same goes for the subfolders in `src` folder.

Second, the name of the module must match the filename. That's why the file containing `Helpers` module is named `Helpers.hs`.

That's it. Now you now how to organize a real Haskell project. Later I'll tell more about modules, but for now that is all you need to know.
