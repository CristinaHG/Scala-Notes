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


## State in objects

objects with state are usually repesented by **objects that have some variable members**. For example, the *bank account* defines a variable *balance* that contains the current balance of the account, and the methods *deposit* and *withdraw* change that variable value.  

```scala
	class BankAccount {
	  private var balance=0
	  def deposit(amount: Int): Unit ={
	    if(amount > 0) balance=balance+amount
	  }
	  
          def withdraw(amount: Int): Int =
	    if(amount > 0 && amount <=balance) {
	      balance=balance - amount
	      balance
	    } else throw new Error ("insufficient funds")
	}
```

Another example, *bankaccount proxy* :

```scala
	class BankAccountProxy(ba: BankAccount){
	   def deposit(amount: Int): Unit = ba.deposit(amount)
	   def withdraw(amount: Int): Int = ba.withdraw(amount)
	}
```

although *BankAccountProxy* does not contain any mutable variable, it is a **stateful object too** as it depends on the history, and so when *deposit* or *withdraw* are called, the balace of the bank account is really changed as originals methods in *bankaccount* would do.






