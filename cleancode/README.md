# Clean Code

This is a summary of the most important and revealing topics from my point of view. These have, for now, been gathered from the _official_ Clean Code books by *Uncle Bob* (Robert C. Martin).

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

### The three laws of TDD

## Logging 
A log message without a dynamic component (think placeholder) is useless.

