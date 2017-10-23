# Loops

## While loops
To model control statements like loops functions are used. **while** is a keyword in Scala, that denotes a kind of loop. We can define **while** using a function:

```scala
	def while(cond: => Boolean)(command: => unit):Unit={
		if(cond){
		     command
		     while(cond)(command)		
		}
		else()
	}
```

as we can see, both *command* and *cond* must be passed by name to be reevaluated in each iteration. As **while** function is tail recursive, it can be implemented in contance stack space, that means: such as eficient as a native while. 

## Repeat loop

```scala
	repeat{
	  command
	}(cond)
```

that kind of loop should repeat *command* until *cond* is true. 
Implementation:

```scala
	def repeat(command: => Unit)(cond: => Boolean)={
		command
		if(cond)() //stops
		else repeat(command)(cond)
	}
```

## For loop

A Java for-loop looks like:

```java
	for (int i=1; i<3; i=i+1){
		system.out.print(i+" ");
	}
	
```

but this can't be modeled simply by high-order function. **For** arguments contain the declaration of "i",  which is visible in other args and in the body of the loop. 

Scala forloop is similar to Java extended for loop:

```scala
	for (i<- 1 until 3; j<- "abc"){println(i+ " "+j)}
```

For-loops translate similar to for-expressions but using *foreach* combinator instead of *map* or *flatmap*. **Foreach** is defined on all collections with elements of type T, as follows:

```scala
	def foreach(f: T => Unit): Unit=
	   //apply f to each element
```

So the for-loop is translated as:

```scala
	(1 until 3) foreach (i => "abc" foreach (j=>println(i+ " "+j)))
```

where for both, the output is: 

```scala
	1a
	1b
	1c
	2a
	2b
	2c
```

### Extended example

lets construct a digital circuit simulator with gates *inverter* , *and* and *or* using classes and functions. As the electronic components have a delay time, they do not change the input inmediately. 
Lets say we've got a class *wire* that models wires. Then we can define the gates as functions like:

```scala
	def inverter(input: wire, output:wire): Unit
	def anGate(a1:wire, a2:wire, output:wire): Unit
	def orGate(a1:wire, a2:wire, output:wire): Unit
``` 

and so we can define a half-adder as:

```scala
	def halfadder(a:wire, b:wire, s:wire, c:wire): Unit={
		val d = new wire
		val e = new wire
		orGate(a,b,d)
		anGate(a,b,c)
		inverter(c,e)
		anGate(d,e,s)
	}
```

a half adder output is 1 just if both inputs are different values. Whith that halfadder, a fulladder can be defined as:

```scala
	def fulldadder(a:wire, b:wire, cin:wire, sum:wire, cout:wire): Unit={
		val s = new wire
		val c1 = new wire
		val c2 = new wire
		halfadder(b,cin,s,c1)
		halfadder(a,s,sum,c2)
		orGate(c1,c2,cout)
	}
```


#### API and usage

Now we give an implementation of this class and its functions which allow us to simulate circuits, based on an API. A digital circuit simulator performs actions. An *action* is a function that doesn't take any parameters and which returns unit: `type Action =() => Unit`

A simulation happens inside an objects that inherits from *simulation trait*: 

```scala
	trait Simulation{
		def currentTime: Int=??? //returns current simlated time as integer
		def afterDelay(delay:Int)(block: => Unit): Unit= ??? //register block of statements to be performed as action after delay 
		def run():Unit=??? //performs the simulation until there are no more actions	
	}
```

##### Interface of gates

The *wire* class must support 3 operations: 

- getSignal: Boolean -> return the current value of signal that is transported by wire.
- setSignal(sig: Boolean): Unit -> modifies the value of the signal transported by wire.
- addAction(a: Action): Unit -> attaches the specified action to the actions of the wire. All of the attached actions are executed at each change of the transported signal. 

```scala
	class wire{
		private var sigVal: false //value of current signal in the wire
		private var actions: List[Action] = List() // actions to be performed when signal change
		def getSignal: Boolean=signal
		def setSignal(s:Boolean): Unit = { // if signal is different from the old one, signal is updated and actions excuted
			if( s != sigVal){
				sigVal=s
				actions foreach (_()) // for(a<-actions) a()
			}
		}
		def addAction(a: Action): Unit)={
			actions= a::actions
			a() 
		}

	}
```

The *inverter* function:

inverter can be implemented by installing an action on its input, so that the action will be performed each time the input change. This action produces the inverse of the input signal on the output wire, after a delay of *InverterDelay* units of time.

```scala
	def inverter(input: wire, output: wire): Unit={
		def invertAction(): Unit ={
			val inputSig=input.getSignal
			afterDelay(InverterDelay){output.setSignal !inputSig}
		}
		input addAction invertAction	
	}	
```

The *and gate* function:

```scala
	def anGate(in1: wire, in2: wire, output: wire): Unit={
		def andAction(): Unit={
			val in1Sig=in1.getSignal //get value of input1
			val in2Sig=in2.getSignal //get value of input2
			afterDelay(AndGateDelay){output.setSignal (in1Sig & in2Sig)}		
		}

		in1 addAction anAction	// whenever one of the wire signal changes the output signal will be recomputed
		in2 addAction anAction	// whenever one of the wire signal changes the output signal will be recomputed	
	}	
```

The *or gate* function:

```scala
	def orGate(in1: wire, in2: wire, output: wire): Unit={
		def orAction(): Unit={
			val in1Sig=in1.getSignal //get value of input1
			val in2Sig=in2.getSignal //get value of input2
			afterDelay(OrGateDelay){output.setSignal (in1Sig | in2Sig)}		
		}

		in1 addAction orAction	// whenever one of the wire signal changes the output signal will be recomputed
		in2 addAction orAction	// whenever one of the wire signal changes the output signal will be recomputed	
	}	
```

The *simutation trait* implementation:

simulation trait must keep in every instance an agenda of actions to perform. An agenda will be a list of simulated events, each of them will be an action with the time it must be produced. The actions to be performed first should be at the beginning, just to pick the 1º element each time.

```scala
	trait Simulation{
		type Action = () => Unit //takes some empty parameter that leads to unit
		case class Event(time: Int, action: Action) //some class of events
		private type Agenda=List[Event]
		private var agenda: Agenda=List()
	}
```

Handling time: we have `private var curtime=0` containing the current simulation time, and we define `def currentTime:Int=curtime` from the beginning, so it is a getter function for `curtime`. 

AfterDelay method:

*AfterDelay(delay)(block)* insert the task *Event(curtime+delay, ()=>block //do the action involved to perform the block)* into the agenda at the right position.   

```scala
	def AfterDelay(delay: Int)(block: => Unit): Unit={
		val item= Event(currentTime+delay, () => block) //creates event at a given time with the given options to perform
		agenda= insert(agenda,item)
	}
```



 
