# Day 1

## Introductions

- Name
- Programming experience
- Scala experience
- Things you want to learn / issues you want to solve


- Noel, 20 years Java / C / Python. 10+ years Scala.
- Sulab, JS / Java / Python. Mostly around devops, observability. Couple of years using Go. 6+ months Scala. Be very comfortable with Scala / Cats / Cats Effects / Monoids etc. Where would apply them? Big data & data processing.
- Jerry, 3 months at ???. Typescript, React, little bit of Go. Goal is to get a foundation of Scala. Team uses Cats Effect.
- Raj, 2nd week at ???. C++ / Java. Get into FP. Background in API development, move in big data processing. Scala for the Impatient.
- Sebastien, 2 years at ???. Scala since 2017. Learned at University (Coursera course). Spark processing, now Cats Effect. Data processing. Python data processing, TS UI. Develop a model of FP.
- Juan, FE engineer (JS). Scala beginner. Java. Looking to learn everything.
- Alex, Scala for 8 years, but mainly data processing / Spark. Recently doing FP, Cats, Cats Effect, http4s. Looking to get deeper into FP stuff.
- Taylor, 2.5 months at ???. 12 years dev. Started in computational science (C++, Python, US Gov). MSc in machine learning. Focused on building backends and services around machine learning. Used Scala but in a very superficial way. Cats, Cats Effect, FP.
- Brian, Java background (5 years). Switched to big data (Scala; Scalding) 3-4 years Scala (big data, APIs) Moved to more of the big data side, Spark, big data. Scala experience but not necessarily indepth knowledge. Learn more of the advanced stuff.

Jerry: deployment infrastructure. APIs, web services, Kubernetes. Scala for backend web server.
Taylor: API backend service for deploying and hosting ML models.
Brian: orchestration of Spark jobs.

Netflix Genie: similar goals.
Building REST APIs. Automate cloud stuff.
Core data platform, hybrid cloud.

## Overview

### General

- Our curriculum is not fixed. We have a rough plan but it will change depending on your interests.
- We have 5 3.5 hr sessions. We'll have a 30 min break about half way through
- Exercises at https://github.com/inner-product/essential-exercises
- This repo at https://github.com/inner-product/mystery-advanced-scala-with-effects-jul-2022


### Rules

1. Cameras / mics on please, unless you have a good reason

2. Pets (and small children) are welcome to join us, but you should introduce them to everyone else. Older children, friends, housemates, partners, etc. are also welcome but don't need to be introduced.

3. You are ultimately responsible for your learning. Three hints:
  1. Ask questions at any time. "I don't understand this and I don't know how to form a clearer question" is a great question!
  2. Collaborate with other students. Explain what you are doing to them. Explain what you're thinking. "I don't know what to do right now" is a great thing to say.
  3. Take notes.


### Curriculum

- Reasoning about effects
- IO basics
- Interpreters
- Type classes


### Goals

- Understand how to design and build large scale systems in Scala.


## Effects

What problem does `IO` solve?
- delay execution of input/output
- error handling
- isolate side effect to compose application

```scala
IO {
  readStuffFromAFile("some-file.txt")
}
IO.block
```

Side effects, substitution, and local reasoning

Functional programming:
1. Reasoning about code
2. Composition
3. Fancy words (for simple ideas)


### Reasoning

Reasoning means understanding code before it is run. Read code and work out what it does.

Substitution
- substitute (replace) an expression with the value it evaluates to (it's result)

```
1 + 1
==> 2

1 + 1 + 1
==> 2 + 1
==> 3

val x = 1
val y = 1 + x
==> y = 1 + 1
==> y = 2
```

Substitution is really really easy. That's the point.

I/O breaks substitution.

```scala
val x = println("Hello")
val y = x

val x = println("Hello")
val y = println("Hello")
```

State breaks substitution. Can't freely substitute when state exists---order of substitution matters.

```scala
val x = 1
val y = x + 2
val z = y + 3 + x

// Can do substitution in any order and get the same result
```

```scala
var x = 1

val y = x + 2
x = 2
val z = y + 3 + x
```

In general, we call things that break substitution "side effects".

Local reasoning: idea you can understand a program fragment in isolation. Know that nothing else can change the meaning of that fragment.

Cannot reason about the below in isolation

```scala
object Example {
  var x = 1
  
  def doSomething: Int = x
}
```

Any other code can change the value of `x` and hence change what `doSomething` does.

Code that doesn't break substitution allows local reasoning.

`var` is bad?
- is `var` bad? (`let` vs `const`)
- why is it in the language then?
  - performance and still allow reasoning?

```scala
def sum(list: List[Int]): Int = {
  var total = 0
  list.foreach(x => total = total + x)
  total
}
```

`total` is not visible outside of `sum` so we can still reason using substitution about uses of `sum`.

Control the machine (which is stateful) vs describing thoughts


### Composition

Composition is building bigger things out of smaller things and ways of combining them.
- we can understand the composition using our understanding of the components and the understanding of the operations that combined them (this is local reasoning)

Builder pattern is basically composition.

Pure functions are functions that always give the same output given the same input.


### IO

We've seen that input / output and state (and also exceptions etc.) break substitution. But need I/O in real programs.

How to handle I/O etc. but still allow reasoning and composition?
- separate description (what you want to do) from action (making it happen)
- descriptions are compositional and substitutable (maintain substitution)
- but once we run a description effects happen and we don't have those properties any more.
- Interpreter pattern

`IO` is a description of that important I/O + concurrency + error handling + resource usage features that the JVM provides. Wraps up all the side effects on the JVM in a compositional and substitutable way.

- `IO`: Cats Effect `IO` monad
- I/O: Input / Output

Typelevel maintains Cats, Cats Effect
- Cats is a library of useful abstractions (like a very abstract standard library)
- Cats is a play on "category theory" where a lot of the abstractions come from (monads, etc.)
- Cats Effect came later. It's `IO` that works nicely with Cats.
- typelevel.org

Companion objects
- A companion object is an object with the same name as a type and in the same file as that type
- Conventionally used for constructors in Scala. Constructors are usually methods on companion objects.

Call-by-name parameters
- `def foo(x: Int)`. `x` is a normal parameter. Argument is evaluated before the method is called.
- `def foo(x: => Int)`. `x` is a *call-by-name* parameter. Argument is evaluated when it is referred to within the body of the method.


## Feedback

- Material at the right pace: Jerry, Brian, Taylor, Alex
- Juan, going ok.

- Monoids, monads, option, either
- Resources 
- Logging (carrying token with IO)
- Tagless final vs IO
  - Abstracting over effect type
- Building scalable / streaming systems

Cats Effect 2 and 3



```scala
package io

import cats.syntax.all._
import cats.effect._

// The goal of these exercises is to introduce some of the basics of using IO:
//
// - constructing IO
// - doing one thing after another (sequencing)
// - combining multiple independent IOs
//
// and additionally get to the fundamental design principle of IO: the
// separation between descrptions and action
object Basics extends IOApp.Simple {
  // 0. This object extends IOApp.Simple. This allows us to run the object. It
  // expects an IO with the name `run` which describes the program it will run.
  // So replace `run` below with whatever you want to see your programs in
  // action.
  // 1. You can create an IO using IO(...) and IO.pure(...). (The former is the
  // apply method on the IO companion object, so can also be written as
  // IO.apply(...))
  //
  // What is the difference between these methods? Can you write a program that
  // demonstrates the difference?
  println("Construction in progress")
  val pureExample = IO.pure(println("Hello1"))
  val applyExample = IO(println("Hello2"))
  println("Constructed IOs")

  // val run = pureExample //IO.apply(println("Hello!"))
  val run = applyExample
  // 2. flatMap allows us to do one thing after another, where the thing we do
  // second depends on the result of the thing we do first.
  //
  // IO.realTime gives the time since the start of the epoch. Using flatMap
  // write an IO that gets the time and prints it on the console.
  def printRealTime: IO[Unit] =
    IO.realTime.flatMap(time => IO.println(time))

  def printRealTime2: IO[Unit] =
    IO.realTime.flatMap { time => IO.println(time) }

  // {} and () around a method body mean the same thing
  // within {} can put multiple expressions and declarations
  // IO.realTime.flatMap { time =>
  //   val now = time // a declaration
  //   IO.println(now)
  // }
  // Can't do this within ()
  //
  // Can also write a partial function (function of a single argument with a
  // pattern match on that argument) within {}
  //
  // val aTuple = (1, 2)
  // Option(aTuple).map{ case (a, b) => a + b }
  // Option(aTuple).map(t => t._1 + t._2) // Yuck!

  // IO[FiniteDuration].flatMap(FiniteDuration => IO[Unit]): IO[Unit]
  // IO[A].flatMap(A => IO[B]): IO[B]
  // F[A].flatMap(A => F[B]): F[B]
  // F[A].map(A => B): F[B]
  // Similar to `then` on Promise in JS (but then is a bit weird)

  // List[A].reduce((B, A) => B): B
  // IO.realTime.flatMap(...).flatMap(...)

  // 3. mapN allows us to combine two or more IOs that don't depend on each
  // other. Here's an example:
  //
  // (IO(1), IO(2)).mapN((x, y) => x + y)
  //
  // When this program is run, are the IOs always evaluated in a particular
  // order (e.g. left to right) or is it non-deterministic?
  //
  // Type class coherence
  for {
    x <- IO(1)
    y <- IO(2)
  } yield x + y

  IO(1).flatMap(x => IO(2).map(y => x + y))

  (IO(1), IO(2)).mapN((a, b) => a + b)

  (IO(1), IO(2)).parMapN(_ + _)

  // 4. The following program attempts to add logging before and after an IO
  // runs. Does it do this correctly?
  def log[A](io: IO[A]): Unit = {
    println("Starting the IO")
    val _ = io
    println("Ending the IO")
  }
  log(IO("Example"))

  def double(x: Int): Int = x + x
  double({ println("Hello 1!"); 1 + 2 })

  def doubleCallByName(x: => Int): Int = x + x
  doubleCallByName({ println("Hello 2!"); 1 + 2 })

  // 5. Write a method that adds logging before and after an IO
  def correctLog[A](io: IO[A]): IO[A] = {
    val start = IO.println("Starting the IO")
    val end = IO.println("Ending the IO")

    for {
      _ <- start
      a <- io
      _ <- end
    } yield a

    // a <- F[A] (gets a value out of an F[A]; becomes a flatMap or map)
    // a = A (introduces a new "variable" or name)

    start.flatMap(_ => io.flatMap(a => end.flatMap(_ => IO.pure(a))))

    start *> io <* end

    (start, io, end).mapN((_, a, _) => a)
  }

  (Option(1), Option(2), Option(3)).mapN((a, b, c) => a + b + c)
  // Generalize to unknown number of elements (with same type) as sequence and traverse

  // 6. What do the *> and <* methods do? Could you use them in the logging
  // method you just wrote?

}
```
