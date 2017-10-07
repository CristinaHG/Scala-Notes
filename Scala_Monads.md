# Monads

Monads are a class of design patters with the following shape:

```scala
trait Monad[T]{
	def flatmap[U]
	def unit[T]
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

