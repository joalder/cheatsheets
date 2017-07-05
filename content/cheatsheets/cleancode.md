+++
date = "2017-06-15T15:21:32+02:00"
draft = false
title = "Clean Code"
+++

This is a summary of the most important and revealing topics from my point of view. These have, for now, 
been gathered from the _official_ Clean Code books by *Uncle Bob* (Robert C. Martin). 
Quotes indicate literal sections from these books.

Clean Code: A Handbook of Agile Software Craftsmanship (ISBN-13: 978-0132350884)

The Clean Coder: A Code of Conduct for Professional Programmers (ISBN-13: 978-0137081073) 

The cheat sheet should be helpful as a guideline in reviews or when in doubt about the cleanliness of some code.


TODO: levels of detail (bad TODO, already forgot what I meant)


## Formatting & Ordering
As a general note, every project should have a clearly defined style of formatting elaborated and followed by the whole team.
Code is usually the easiest way to describe these rules.

### Dependant Functions
Dependant functions should be close to each other and in the order of their call, if possible. Helps with readability and natural flow. 
Nice example: Static factory methods on top before private constructor.

### Conceptual Affinity
Even if the functions have no direct dependency, they might be conceptually very similar and should thus be grouped/close.

### Horizontal Alignment
Spacing e.g. the types and names of member variables of a class can be suboptimal 
as it may disconnect the relation between the names and type of a variable.
If there are such long lists of variables or assignments it's probably a hint to split the class/function.


## Objects & Data Structures

Nice differentiation about data vs. object 

> Procedural code (code using data structures) makes it easy to add new functions without changing the existing data structures. 
> OO code, on the other hand, makes it easy to add new classes without changing existing functions.

Complement:

> Procedural code makes it hard to add new data structures because all the functions must change. 
> OO code makes it hard to add new functions because all the classes must change.

In essence:
- Objects expose abstractions and functions
- Data structures expose their data (e.g. data transfer objects DTO)

Goal is to have a clear separation between these to types. Can also be seen as data vs. business logic.


## Law of Demeter

Train wrecks and stuff. Not really sure about that yet.. 
Law of Demeter only applies to objects and not to data structures.


## Exception Handling

Write try-catch-finally first, test it, narrow and refine it.

Add meaningful messages to each and every exception you throw.

Always think of exceptions form the perspective of the caller/catcher. Only create different exception classes if you need to catch it at different places.

Special case objects instead of exception may be a nicer flow for the caller.

Don't return null, throw exceptions or return special case. In Java8 there would even be the possibility to wrap the whole thing into an Optional to make the handling easier.

### Checked Exceptions
The war is over, checked exceptions lost, move on.

In general their violation of OCP is the most important drawback. It leads to massive decapsulation (seems like this is no CS term, but I'll use it anyway) in your code base. 
It might be still handy in a few situations, but be sure to bring lots of arguments for your use!

Also frameworks like Spring use unchecked exceptions pretty much exclusively and even wrap checked into unchecked ones for you. 


## Boundaries
Encapsulate mutable objects if necessary. Possibly wrap them into immutable of needed to pass anyways.
 
### Learning Tests
Nice idea to validate possible use, ensure functionality and learn the API of third party library.


## Testing

Build - Operate - Check should be clearly visible when looking at tests

A (unit)test should be **fast** to execute, **independent** of each other, **repeatable** in any environment, 
**self-validating** meaning they should know if they passed or failed and **timely** written.

### The three laws of TDD

> 1. You may not write production code until you have written a failing unit test.
> 2. You may not write mor of a unit test than is sufficient to fail, and not compiling is failing.
> 3. You may not write more production code than is sufficient to pass the currently failing test.

### Single Assert
Some say, that there should only be a single assert per test. However this can lead to a violation of the DRY principle. 
Probably better to say that the number of asserts should be minimized.

## Classes

### Basic Layout
By Example:
```java
public class Layout {
    public static final String CONSTANT = "CONSTANT";
    private static String variable;
    private int counter;
    
    public void doStuff(){
        util();
    }
    
    private void util(){
        // Do more stuff
    }
    
    public void doOtherStuff(){
        // Other stuff
    }
    
}
```
This follows the newspaper approach, the whole class should read like a story, from very general to the last detail

### Responsibilities
Responsibilities of a class should be minimized. 

> We should also be able to write  a brief description of the class in about 25 words, withouth using the workds "if", "and", "or" or "but".

SRP says:

> ... a class or module should have one, and only one, reason to change!

### Cohesion
The more variables of a class its methods use, the more cohesion is there. Cohesion should be high.
Keeping functions small and parameter lists short, can reveal a grouping of functions and variables. This is a perfect opportunity to split the class.

## Logging 
A log message without a dynamic component (think placeholder) is useless.

