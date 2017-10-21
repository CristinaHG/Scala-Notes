# Identity and change

assigment poses the new problem of deciding if two expressions are or not the same: `val x= E ; val y= x ` we assume that *x* and *y* are the same, so that is like writing: `val x= E ; val y = E` an that is called **the referential transparency property**. 

**But** once we do the assigment, are the objects really the same? 

For example, if we do: 

```scala
	val x = new Bankaccount
	val y = new Bsnkaccount
```

we can see they're different as they're different objects. To demostrate it, lets define whats "being the same". "Being the same" is defined by the **Operational equivalence property**: "given to definitions, x and y, we say x and y are operationally equivalent if no possible test can distinguish between them". To prove it:

- 1. Execute the definitions followed by any sequence of operations that involves both x and y
- 2. Execute the definitions with another sequence S' obtained by replacing all ocurrences of y by x in S.
- 3. If all possible pairs of (S,S') produce the same result, then x and y are the same. Else they are different.

