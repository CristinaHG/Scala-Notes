# Functions and state

Until now our programs have been side-effect free, that is, **time** wasn't important, so any sequence of actions would have given the same results. *Rewriting can be done anywhere in a term, and all rewriting which terminate lead to the same solution.* Is an important result of lambda-calculus (theory behind functional programming)

Example:
Lets say we have the following definitions for *iterate* and *square* functions:

```scala
	def iterate(n: Int,f: Int => Int, x:Int)=
	  if(n==0) x else iterate(n-1,f,f(x))

	def square(x:Int) = x*x
```

so the expression:


```scala
	if (1==0) 3 else iterate(1-1,square,square(3))
```

could be rewritten as 

```scala
	iterate(0,suqare,square(3))
```

but also as

```scala
	if(1==3) 3 else iterate(1-1,square, 3*3)
```

## Stateful Objects

we often describe the world as a set of objects, and some of them have a state that changes over time. So when does an object have a state? when the object behaviour is influenced by its history. 

### Definitions:
 - **Stateful objects**: when their state depends on the history. For example: an object **bank account** has a state, as it vary over time.
 - **Statefulness objects** :  the object does not vary over time.


## State implementation

Every form of **mutable state** is constructed from variables. They're written as a value definition but using **var** isntead of **val**. 
So if we want an inmutable stte object, we will declare it as `val x: String = "Cristina"` but if what we want is a mutable state object, we will declare it as `var x: String = "Cristina"`.
Both, value and variable definition associates a value with its name, but the difference is that with variable definitions tha association can be changed later, for example, if we now do: `x="Alex"` , `x` will change its value to 'Alex' just if x is declared as a var. If declared as val, x will still keep the value "Cristina" after the "Alex" assigment. 

