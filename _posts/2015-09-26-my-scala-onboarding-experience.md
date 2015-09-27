---
layout: post
title: My Scala Onboarding Experience
date: 2015-09-26 15:00
feature-img: "img/sample_feature_img.png"
---

The first thing that fascinates me is Martin Odersky's pronouncement that
functional programming (FP) and object-oriented programming (OOP) are
orthogonal concerns. The idea is that you can do OOP in imperative as well as
in functional languages; there's no need for a great divide between the
functional and OO realms. So where functional languages like F#, OCaml, and
Haskell normally avoid the use of objects, Scala embraces them. That's pretty
wild!

This attitude fits pretty well with the history of OO in languages like Lisp.
Going further, Odersky even considers Smalltalk, as purebred an OO language as
it gets, to be a member of the broader family of functional languages, thanks
to its support for blocks and hence arbitrary control structures.

So the big idea behind Scala is that while functional programming is generally
preferable to imperative programming, since FP isn't always feasible, we might
as well have a nice language for both. And while we're at it, make it part of
the JVM ecosystem.

### From a Haskeller's perspective

We can't always rely on the functional style. That's true. Some data structures
and algorithms are known only in imperative forms; and for others, the
functional equivalents have unacceptable complexity. However, every practical
functional language has some form of reference type to support operations that
require mutation.

#### OOP

You can do OOP in Haskell, but the ways I know of are either slow or have the
occasional strange edge-case. And error messages may be a touch on the
obscure side.

#### Signalling purity

Haskell's `do` notation, reference types, and associated monads provide a
different and highly principled way of mixing functional and imperative-like
programming. What you lose in flexibility, namely the ability to have
side-effects anywhere (ignoring Haskell's unsafe forms), you gain in code
clarity. One look at a function's type signature tells you whether it can have
side-effects or not. That clarity is missing in Scala.

In Haskell, it's obvious when a function is pure. In Scala, you have to check.

Scala uses `val` for immutable values and `var` for mutable references. But
the object captured by the immutable form can itself by mutable. So `val` and
`var` really don't tell you that much. (As an aside, it would have been nice
had `var` been called `ref` instead. As is, the two are similar visually,
and depending where you're from they even sound the same. I guess the
similarity is intentional.)

Scala has a neat shorthand for procedure-style functions, those
functions that are executed purely for their side-effects:

```scala
  def doThis(a: Int) { println(a) }
```

Skipping the `=` indicates the return type is `Unit`, which fulfills the same
role as Haskell's `()`. But while a `Unit` return type signals a side-effecting
function, a non-`Unit` return doesn't signal a pure function.

#### REPL

Well, the Scala Repl kind of sucks. [Apparently it's the JVM's fault](http://stackoverflow.com/questions/2471947/is-there-an-easy-way-to-get-the-scala-repl-to-reload-a-class-or-package). The JVM
doesn't make reloading stuff easy at all, so there's no `:reload`.
There is a `:replay` which reruns the Repl session from the top. But it doesn't
reload any imported modules either.

On the plus side, there are worksheets.

#### Modules

Scala modules are first-class, which is to say, supremely awesome.

#### Return a smile :)

If you follow the spacing convention of `<typedSomething: Type>` everywhere,
you end up with little sad faces in every function definition:

```scala
  def fun(arg: TypeA): TypeB = {...}
```

Can you see the frown? Oh well.

#### Type annotations

Scala is part of the inlined type annotation tradition. While
Haskell's

```haskell
  fun :: TypeA -> TypeB
  fun arg = ...
```

is more verbose, it's great for reading and reviewing code. First get a
handle on the types, then look at the particulars.

#### Type inference

For type inference, Scala's type system isn't as complete as Haskell's.

Here's an error I ran into:

```scala
    for {
      size <- Gen.choose(1,30)
      set = DisjointSet(size)
      numU <- Gen.choose(0,2*size)
      pair = for { a <- Gen.choose(0,size-1)
                   b <- Gen.choose(0,size-1) } yield (a,b)
      unions <- Gen.containerOfN(numU, pair)
    } yield unions.toArray.foldLeft(set) {
      case (s,(i,j)) => s.union(i,j)
    }
```

Find the compile-time error and its cause!

Error:

```
No implicit view available from C[(Int, Int)] => Traversable[(Int, Int)].
      unions <- Gen.containerOfN(numU, pair)
                                ^
```
The cause is a missing type annotation:

  > Gen.containerOfN**[List,(Int,Int)]**(numU, pair)


The type inference being a little more limited means we need a few
more annotations here and there. But the compiler error pointed
to the right spot, so that's pretty good.

#### Advanced stuff

If you love obscure features both Haskell and Scala have you covered. With
Haskell's language extensions you can program in a different language every day
of the week and yet `ghc` knows how to compile them all. Scala is a pretty
large language. For example, it supports implicit parameters, implicit classes
to extend existing types, using objects as modules, and abstract classes.

