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


