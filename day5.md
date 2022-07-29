# Day 5

- `Either`, `IO`, and error handling

- `Option[A]` is either `Some` or `None`
   - `Some` has a value of type `A`
   - `None` has no value
- Either
  - `Try[A]` is either `Success` or `Failure`
     - `Success` has a value of type `A`
     - `Failure` has a `Throwable`
     Algebraic data type!

  - `Either[A, B]` is either a `Left` or `Right`
    - `Left` has a value of a type `A`
    - `Right` has a value of a type `B`
    
    Also an algebraic data type!
    By convention `Left` holds a failure case, and `Right` holds a success case. (Called "right biased")
  - fail-fast error handling with Either
    fail-fast = stop as soon as you hit an error
    - creating a success or failure
    - dealing with success
    
    `Either[Error, Success] ??? (Success => Success2): Either[Error, Success2]`
    `Either[Error, Success] map (Success => Success2): Either[Error, Success2]`
    Fix the `Error` type (i.e. it doesn't vary)
    `F[Success].map(Success => Success2): F[Success2]`
    `F[A].map(A => B): F[B]`
    
    `Either[Error, Success] ??? (Success => Either[Error, Success2]): Either[Error, Success2]`
    `Either[Error, Success] flatMap (Success => Either[Error, Success2]): Either[Error, Success2]`
    `F[A].flatMap(A => F[B]): F[B]`

    - dealing with failure
    
    Algebraic data type ... use structural recursion.
    - Pattern match on `Left` and `Right`.
    - use a method defined on `Either`
      - `getOrElse`: get the value if it's `Right` otherwise use some default. 
      - `orElse`: return `this` if it's `Right` otherwise try a fallback (which can fail).
    
  - accumulating errors with Either
    - `parMapN` etc. accumulate errors
      - where the error type has a `Semigroup` / `Monoid`
    - `NonEmptyList` / `NonEmptyChain` represents 1 or more elements, so useful to represent errors. 
    
  Either is best for:
    - structured error handling
      - you know that different kinds of errors that could occur and you want to do something meaningful with the different kinds
    - accumulating errors
- Errors in IO
  All IOs can hold an exception. Exceptions raised within an IO give fail fast error handling.

  - fail-fast
  - accumulating
    No particular support. `IO[Either[AccumulationType, Success]]`, can use `EitherT` to make it a bit easier to work with (see Scala with Cats).
  - recovery
    `handleError` and `handleErrorWith`. Latter can fail while trying to recover (returning an `IO`).
    
    Very similar model to exceptions: throw exceptions to fail and use `handleError(With)` to install exception handlers.
- Error handling strategies
  Who cares about this error:
  - programmer: wants to know line numbers, etc. to debug code
  - end user: wants to know if they can change their actions to succeed

  What can the program do about the error:
  - nothing: most errors fall into this category
  - something: most errors are data validation errors
  
  Most errors can be represented by an exception:
  - fail fast: can't do anything about most errors
  - useful information for programmers / ops to debug
  
  Much smaller category have structure and address end users:
  - `Either`, error accumulation, is useful
  
  As you rise through layers of the program error information becomes less useful.
  
  `IO[Either[MeaningfulErrorMessage, A]]`
  
  ```scala
  class MeaningfulErrorMessage extends Exception()
  ```
  `IO[A]` but you must remember to handle meaningful errors specially.


Essential Scala: Scala fundamentals. It's a bit old but still relevant
- Coursera courses are ok. A bit mathematical.
- Other books. I haven't read most of them.
  - The Scala Programming Language is a good reference but not good to learn from
  - Functional Programming in Scala is a bit old, interesting style. 

Scala with Cats: Cats, Type classes. 
- Slowly being updated

Essential Effects: IO.

Missing is material about interpreters.

Gabriel Volpe has a book covers more intermediate / advanced material. Might be a worth a look.

Structure and Interpretation of Computer Programs (SICP)
- PLAI https://plai.org 


https://www.inner-product.com/training/feedback/
