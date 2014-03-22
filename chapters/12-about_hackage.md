### About a hackage ###

If you worked with Linux, you should be familiar with а concept of a "repository":
a sort of centralized place from which you can get a lot of goodies. Hackage is the
main repository in the world of Haskell.

The name comes from the merging words **H**askell and p**ackage**. It exists
since year 2008 and it is actually a huge heap of packages. You may read "package"
as "library", but in the world of Haskell the common concept is "package".

Inside that heap you will find a lot of ready-made solutions, both for the common or
highly specialized problems.

In order to use a package you need to complete four steps:

1. find this package,
2. install it,
3. add it to your project,
4. import the required modules from it.

#### Finding ####

I recommend you to search for packages here:

1. [Hoogle](http://www.haskell.org/hoogle)
2. [Hayoo!](http://holumbus.fh-wedel.de/hayoo/hayoo.html)

Just type the required name, or category, or some associative word in the search
box and you get a lot of interesting results.

For example, let's install package `text`, an advanced package to work with...
text. Surprise, surprise.

#### Installing ####

There is an instrument for a convenient installation of package from Hackage,
its name is `cabal`. Oh yeah, the same, already known to us utility!

Change directory to the root of our project and run a command:

	$ cabal update

With this command we updated the list of available packages. It is recommended
to run this command from time to time, in order to stay on the "cutting-edge"
of the Hackage development.

After package list is updated we can install the required package:

	$ cabal install text

A bit of patience and the package is installed.

#### Adding to the project ####

Open build file `Real.cabal` and add the name of the installed package to it.
In order to do that, find parameter `build-depends` and append the name
of the package after the comma:

	build-depends:    base == 4.6.*, text

And the last step.

#### Importing modules ####

Package consists of modules (and modules, as you already know, are files with
source code). There are quite many modules in the `text` package, we will take
the first one, `Data.Text`. Open `Main.hs` and write in the beginning:

```haskell
import Data.Text
```

Ready. Now we can use different goodies from that module. In the nearest future
you will find out which of them we are going to use.

#### About Prelude ####

There is a standard module which is imported into all your modules by default.
Its name is `Prelude`. It contains the most basic Haskell instruments, you will
use many of them all the time.
