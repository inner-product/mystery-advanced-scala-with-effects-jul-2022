# Day 4

## Plan

Interpreters!


General idea of interpreters
- core idea in FP: separation of description and action
- interpreter carries out the actions

DSL / algebras components
1. Constructors: something else => our description type
   - usually on the companion object
2. Combinators: description => description
3. Interpreters: description => something else

Something else = something that is not a `Stream`
Our description type = `Stream`

Programming strategies for implementation
- reification
  - abstract meaning: to make concrete what was abstract
  - concrete meaning: is to turn combinators and constructors into data structures. Then interpreter walks the tree of data structures (abstract syntax tree).

Reification:
1. Reify constructors and combinators
2. All method parameters are stored in the data structure *including `this` when available*
3. The data structure extends the result type of the method

Aside:
- sealed trait and (final) case classes?
- algebraic data types?
  - represent data in terms of:
    - logical ands (a Macbook is a CPU and a SSD size and a case size and RAM)
    - logical ors (a Mac is an iMac or a Macbook Air or a Macbook Pro)

```scala
// A is a B and C
final case class A(b: B, c: C)

// A is a B or C
sealed trait A
final case class B() extends A
final case class C() extends A

sealed abstract class A
final case class B() extends A
final case class C() extends A
```

FP: care about reasoning. Sealed improves reasoning (because you know all the possible cases)

OO                                 vs FP
trait                              vs sealed trait (algebraic data type)
- easily add new implementations   - cannot easily add new implementations (data structures)
- cannot easily add new methods    - easily add new methods

(Want both? Expression problem. Tagless final solves this.)

Structural recursion:
- Every algebraic data type has a unique structural recursion (aka fold)
- Using structural recursion you can implement any transformation on an algebraic data type

Implementation of structural recursion in Scala:
1. Pattern matching (FP)
2. Dynamic dispatch (OO)


Pattern matching is an expression (evaluates to a value)

```scala
anExpression match {
  case guard1 => rhsExpr1
  case guard2 => rhsExpr2
  ...
}
```

1. Evaluate `anExpression` to produce a value
2. Test the value against the guards from top to bottom
3. Evaluate the RHS expression for the first guard that matches the value

The overall result is the result of the RHS expression that is evaluated.

Guards are the patterns we check the value against. A pattern can be:
- a literal like "hello" or 1, which succeeds if the value is equal to the literal
- any unquoted string is a wildcard that matches anything and gives the matched value that name is the RHS expression
  ```scala
  1 match { case aNumber => aNumber + 2 } // = 3
  ```
- for case classes the pattern syntax matches the syntax for constructing a case class, with patterns for the constructor arguments
- _ is a wildcard that doesn't give a name to the matched value (i.e. it means "I don't care")

Defaults:
- Exhaustivity checking: the compiler shouts at you if you forget a case for an algebraic data type.
- Can use a wildcard or _ as a default (but can be dangerous as the compiler won't let you know if you need to update the match if cases are added or removed from an algebraic data type)
- Otherwise don't need a default but this will fail at run time if no patterns match.

Recursion rule that puts the recursion into structural recursion:
- when the data is recursive the method is recursive in the same place

Follow the Types!
- Look at the types of the available inputs
- Look at the type of the desired output
- Work out how to go from one to the other
