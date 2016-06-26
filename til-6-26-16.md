# JavaScript Operator Precedence Under the Hood

[the kata in question](https://www.codewars.com/kata/noobcode-02-tricky-questions-primitives-and-operator-precedence)

When trying out this CodeWars kata, which has the following description: 

```
1 < 2 < 3 === true, right? but can

3 < 2 < 1 === true, too??

Here's your task: write a function greaterThanLessThan with three arguments (a,b,c) that returns a boolean (true or false), such that

a = 1
b = 2
c = 3
returns true, as does

a = 3
b = 2
c = 1
``` 
### Why does ```3 < 2 < 1``` eval to true?

In operator precendence in JS, the strict equality evals after the comparison operators. My first guess was that the equality actually had precedence and so 1 === true was being evaluated first, which a true code make sense for because JS is weird about type equality, but the cruz here is that it *isn't* [when you're using the strict equality operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Comparison_Operators#Identity). That one doesn't do the type conversion that results in weird boolean results of checking a string to an integer for equality, for example. 

So, I was still on the wrong track. So then, why, is ```3 < 2 < 1 === true```...well...true? 

A quick sanity check is 2 < 1. That evaluates to false, exactly like it should. So does 3 < 2. So then, the heart of the problem is somewhere in there being 3 integers and two comparisons, in trying to put it all together. 

Code still evaluates left to right after you figure out your precedence and I was forgetting that. So, I was stuck on bouncing all around the statement, trying to figure out what is evaluating where, when after you know that ```<``` takes precedence over ```===```, then you can just read left to right. 

The problem is that JS is still weird about types, so, ```3 < 2``` evals to false, confirming my sanity, BUT, ```false < 1``` evaluates to true, which is the heart of the problem. 

### Why does ```false < 1``` evaluate to true?  

The heart of the problem lies here: 

> One of the conveniences in JavaScript is that it will automatically coerce any value being referenced in a boolean context to a boolean value. But there are cases where this can be as confusing as it is convenient. 

Since we are in a boolean context, 1 is being coerced into true. So: 



## In summary: 

First glance application of common sense tells us that this shouldn't be true but it is: 

3 < 2 < 1 === true; 

Deep dives into how operator precedence works, how primitives work, and the existence of JS type conversion reveals that this is the order of evaluation and type coercion that's happening: 

1. 3 < 2 < 1 
2. 3 < 2 => false 
3. false < 1 
4. false < true => true
5. 3 < 2 < 1 ultimately evaluates => true 
6. true === true => true 

## The final question is why is false < true? 

True isn't < false. 

```true < false``` evaluates to false, but ```false < true``` evaluates to true. 

THIS IS A HUGE WTF. We're not asking equality, we're comparing. So then, how does the comparison operators work under the hood in JS? 

Another explanation and reiteration of what JS is doing under the hood with comparison operators is: 

> If the two operands are not of the same type, JavaScript converts the operands then applies strict comparison. If either operand is a number or a boolean, the operands are converted to numbers if possible; else if either operand is a string, the other operand is converted to a string if possible. If both operands are objects, then JavaScript compares internal references which are equal when operands refer to the same object in memory. 

	- from [this Stack Overflow](http://stackoverflow.com/questions/7615214/in-javascript-why-is-0-equal-to-false-but-when-tested-by-if-it-is-not-fals)


So, in my final conclusion, I think that, since true and false are both booleans type, then JS under the hood is comparing the internal *REFERENCES* to true and false and true's reference is smaller or before false's. 

Mysteries solved. Now to go solve the actual problem I was really working on in the first place. 

This is just another example of why I really really hate JavaScript's implicit type conversion and how much it can bite you if you're not carefully aware of it.