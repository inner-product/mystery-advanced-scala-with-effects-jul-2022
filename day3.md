# Day 3

## Plan

- Type classes
- Type classes in Cats
  - Monoid / Semigroup
  - Applicative (Semigroupal)
  - Monad
  - Traverse


## Extension Methods

Adding method to a type
- often, but not always, used with type classes
- language feature: `implicit class`

```scala
implicit class IntOps(x: Int) {
  def times(f: => Unit): Unit =
    0.to(x).foreach(_ => f)
}
```

Amazing for confusing colleagues (and ensuring job security)

If we call a method on an object which does not have such a method then
- the compiler searches for implicit classes in scope
- if any implicit class defines the method it constructs an instance of the implicit class and calls the method

Useful for working with type classes.

```scala
implicit class JsonOps[A](a: A) {
  def toJson(implicit w: JsonWriter[A]): Json =
    w.write(a)
}
```

Best practices:
- only use for a few well defined methods: the core things in your library
- usually in conjunction with type classes


## Type Classes Cats

- Semigroup / Monoid
- Applicative (Semigroupal)
- Monad
- Traverse


### Monoid

A set A has a monoid if we define:

- an identity element 0; and
- a binary operation +: (A, A) => A

such that

- x + 0 == x == 0 + x, for all x in A (commutative identity)
- (x + y) + z == x + (y + z), for x, y, z, in A (associativitiy)


(x + y) + (z) == (x) + (y + z)
A pair of brackets = running on one machine
Associativity says we always get the correct answer no matter how we distribute work (so long as order is maintained)

(A commutative [x + y == y + x] and associative operation means we don't have to care about order at all.)

Types have monoids, not are monoids
Int, +, 0
Int, *, 1
non-negative Int, max, 0

CRDTs

Using cats:

`import cats.implicits._`

Cats monoid operation:

`a1 |+| a2` <- the monoid combine / addition operation

`List[A].foldLeft(B)((B, A) => B)`
- The function you pass to foldLeft `(B, A) => B` has the same type as the transition of a finite state machine
- The function has the same type as a "monoid action".
