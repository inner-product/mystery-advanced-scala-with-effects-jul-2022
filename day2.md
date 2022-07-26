# Day 2

## Plan

- Parallelism with `IO`
- Type classes


## Parallelism

- `parMapN`
- `sequence`, `traverse`, `parSequence`, `parTraverse`

`mapN` is sequential
`parMapN` is parallel

`mapN` expect a known number of elements (`IO`s), but each `IO` can contain a different type of a value. Like a tuple.

`sequence` and `traverse` which work with an unknown number of `IO`s (0 or more) but each `IO` must have the same type of value. Like a `List`.
- `List[IO[A]].sequence: IO[List[A]]`
- `List[A].traverse(A => IO[B]): IO[List[B]]`

List[A].traverse(A => IO[B]) = List[A].map(A => IO[B]).sequence


## Type Classes

Implicits:
- implicit parameters
- implicit values
- implicit classes
- implicit conversions (evil; we won't speak of them again)

Type classes
- type class
- type instances
- type usage

Implicit parameters:
- Implicit parameters are parameters for which the compiler will provide arguments if no arguments are explicitly provided.
- Methods in Scala can have multiple *parameter lists*.
- The last parameter list of a method can be implicit. Start it with the keyword `implicit` and all parameters in the list become implicit parameters.
- If the programmer doesn't explicitly provide an argument for an implicit parameter that compiler looks for *implicit values* in the *implicit scope* that have the same type as the parameter. If it finds an *unambiguous* match it will provide that argument for the parameter.

```scala
def anExample(x: Int)(implicit y: Int, z: Int): Int =
  x + y + z
  
anExample(1)(2, 3) // explicit arguments, works as normal
anExample(1) // compiler looks for implicit values
```

Implicit values:
An implicit value can be either
- `val`
- `object`
- `def` with *only implicit parameters*
that is preceded by the keyword `implicit`

```scala
implicit val aNumber: Int = 42
implicit object Cat { name = "Henry" }
implicit def addImplicitly(implicit x: Int, y: Int): Int = x + y
```

Implicit scope:
- Where the compiler looks for implicit values.
- Normal (lexical) scope. Things in enclosing braces above the current point in the code + things imported into scope.
- Additionaly, companion objects for any types involved in the implicit parameter (the type of the parameter + the type of whatever the method is defined on)

```scala
class Sort {
  // Will look in companion object of `Sort`, `Ordering`, and `A` for `Ordering[A]`
  def quickSort[A](elements: List[A])(implicit ordering: Ordering[A]): List[A]
}
```

Implication: put implicit values in companion objects so they can be found without having to explicitly import them.

Implication #2: preferrably only one implicit value for a given type.


Implicit classes:
- add extension methods to types. Often use with implicit parameters and values (e.g. `mapN` in Cats) but they're not essential.


Type classes
- Problem we're solving is: adding functionality to a type after the type is declared. Compare to OO mixins / extension: can only be done at the point where the type (class, trait, etc.) is declared.

E.g. add functionality to convert to JSON. Requires adding functionality to core types like `Int` and `String` which we cannot change.

Components of a type class:
- type class itself
  - declares an interface; the functionality we're providing
  - a `trait` with at least one type parameter (generic type)
- type class instances
  - implementations of the type class for a specific type
  - implicit values
- type class use
  - where we require a type class instance so we can access some functionality
  - implicit parameter
  
```scala
// Type class
trait Ordering[A] {
  def min(x: A, y: A): A

  def max(x: A, y: A): A = {
    val result = min(x, y)
    if (result == x) y else x
  }
}
object Ordering {
  implicit val intOrdering: Ordering[Int] =
    new Ordering[Int] {
      def min(x: Int, y: Int): Int =
        x.min(y)
    }
}

object Sort {
  def bubbleSort[A](list: List[A])(implicit ordering: Ordering[A]): List[A] =
    list match {
      case Nil => Nil
      case a :: b :: rest => ordering.min(a, b) :: bubbleSort(ordering.max(a, b) :: rest)
      case a :: Nil => a :: Nil
    }
}
object Example {
  implicit val otherIntOrdering: Ordering[Int] = ???
  Sort.bubbleSort(List(1,2,3,5,2,0))
}
```
