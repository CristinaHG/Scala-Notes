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

## Monad's algebraic laws

To be a monad, besides of the shape, it should satisfy the following propierties:

1. Associativity:
`m flatmap f flatmap g == m flatmap(x=> f(x) flatmap g)`
2. Left unit: 
`unit(x) flatmap f == f(x)`
3. Right unit:
`m flatmap unit == m`

## Examples

- **List** is a monad with `unit(x)=List(x)`
- **Set** is a monad with `unit(x)=Set(x)`
- **Option** is a monad with `unit(x)=Some(x)`
- **Generator** is a monad with `unit(x)=single(x)`

where **flatmap** is an operation on each of these types but **unit** is different for each monad, that is, each monad has a different expression that gives the unit value.


