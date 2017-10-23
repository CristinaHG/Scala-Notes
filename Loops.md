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

For-loops translate similar to for-expressions but using *foreach* combinator instead of *map* or *flatmap* :

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






