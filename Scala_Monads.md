# Monads

## Definition
Monads are a class of design patters with the following shape:

```scala
trait Monad[T]{
	def flatmap[U](f: T => Monad[U]): Monad[U]
	def unit[T](x: T): Monad[T]
}
```
which main idea is to provide a standard interface for composing and sequencing operations on some contained values.
As **map** can be defined for every monad as a combination of *flatmap* and *unit* as:

```scala
m map f == m flatmap (x => unit(f(x)))
```

or

```scala
m map f == m flatmap(f andThen unit)
```

it its said that a Monad's shape is the following:

```scala
trait Monad[T]{
	def map[U](f: T => U): Monad[U]
	def flatmap[U](f: T => Monad[U]): Monad[U]
}
```

together with some algebraic laws that they should have.
If appart from **flatmap** they also define **withFilter** they are called **"monads with zero".**

## Monad's algebraic laws

To be a monad, besides of the shape, it should satisfy the following propierties:

1. Associativity:
`(m flatmap f) flatmap g == m flatmap(x=> f(x) flatmap g)`
2. Left unit: 
`unit(x) flatmap f == f(x)`
3. Right unit:
`m flatmap unit == m`

**Monoids**: a simple form of monads that doesn't bind anything (easier domain). For example, *Integers* are a monoid. Lets check they have all the needed propierties:
1. Associativity:
`(x+y)+z=x+(y+z)`
2. Left unit:
`x flatmap f = f(x)`
3. Right unit:
`x flatmap unit=x`

## Examples

- **List** is a monad with `unit(x)=List(x)`
- **Set** is a monad with `unit(x)=Set(x)`
- **Option** is a monad with `unit(x)=Some(x)`
- **Generator** is a monad with `unit(x)=single(x)`

where **flatmap** is an operation on each of these types but **unit** is different for each monad, that is, each monad has a different expression that gives the unit value.

Another example:

```scala
abstract class Option[+T]{

	def flatMap[U](f: T => Option[U]): Option[U] = this match {
	  case Some(x) => f(x) // gives optional value
	  case None => None
	}
}
```
is a monad as it verifies:
1. Associativity: `(opt flatmap f) flatmap g == opt flatmap (x=> f(x) flatmap g)`

```scala
	(opt flatmap f) flatmap g
	== (opt match { case Some(x) => f(x) case None => None })
	match {case Some(y) => g(y) case None => None } //second flatmap
```

```scala
	opt match{
		case Some(x) =>
		  f(x) match {case Some(y) => g(y) case None => None }
		case None =>
		  None match { case Some(y) => g(y) case None => None }
}
```

2. Left unit: `Some(x) flatmap f` as Some(x) matches case Some(x) and that is `f(x)`
3. Right unit: `opt flatmap Some` opt could match Some or None. If Some is matched, Some(x) would be return else none would be return. In each of the two cases we turn exactly the thing we started with, that is ,opt.

Another example, **Try** :

```scala
	abstract class Try[+T]
	case class Success[T](x: T) extends Try[T]
	case class Failure(ex: Exception) extends Try[Nothing]
```

you can wrap up some comptation in a Try: `Try(expr) //gives Success(someValue) or Failure(someException)`. So an implementation for **Try** could be:

```scala
	object Try{
	  def apply[T](expr: => T): Try[T] = //expression passed by name (arrow indicates call by name parameter)
	    try Success(expr)
	    catch {
		case NonFatal(ex) => Failure(ex) //runtime exceptions, normal exceptions
	    }	
	}
``` 
composing it as a for expression:

```scala
	for {
	  x <-computeX
	  y <-computeY
	}yield f(x,y)
```
if computeX and computeY both succeed, that will return a success value with the value of f(x,y), but it computation fails with an exception, will return Failure(ex) with the first exception that went wrong.
To support the notation above, we must define **flatmap** and **map** on the **Try** type:

```scala
	abstract class Try[+T]{
		def flatMap[U](f:T => Try[U]): Try[U] = this match {
        	  case Success(x) => Try f(x) catch { case nonFatal(ex) => Failure(ex) }
	          case fail: Failure => fail
		}

		def map[U](f:T => U): Try[U] = this match {
		  case Success(x) => Try(f(x))
		  case fail: Failure => fail
		}}
```

So for a Try value, t :

```scala
	t map f == t flatMap (x => Try(f(x)))
		== t flatMap (f andThen Try)
```

but is **Try** a monad with `unit=Try` ? **Not really** because the left unit law fails as `Try(expr) flatMap f != f(expr)` as the left part will never raise a non-fatal exception whereas the right part will raise any exception thrown by **expr** of **f**. 

*Bullet-proof principle: "An expression composed from Try, map ,flatMap will never throw a non-fatal exception"*

## Meaning of the laws for For-Expressions
monad typed expressions are typically written as for expressions, so what laws are saying in that case is:
1. Associativity: says that

```scala
	for(y <- for (x<-m; y <- f(x)) yield y
	z <- g(y)) yield z 
``` 

is the same as: 

```scala
	for (x <-m;
	     y <-f(x)
             z <-g(y)) yield z
```
that is, that we can "inline" nested for expressions, providing a bigger one.

2. Right unit: the trivial for expression that generates x and return it, is the same as the original value.

```scala
	for (x <-m) yield x == m `
```
3. Left unit does no have analogue for for-expressions. only associativity and Right unit is important for for-expressions behaviour.


