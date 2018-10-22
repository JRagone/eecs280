Class 13
========

## Logical short-circuiting

The `||` and `&&` operators short-circuit

|Operator|LHS|Result|
|---|---|---|
|`||`|1|RHS not evaluated|
|`&&`|0|RHS not evaluated|

``` cpp
#include <iostream>
using namespace std;

int one( )
	{
	cout << "in one" << endl;
	return 1;
	}

int zero( )
	{
	cout << "in zero" << endl;
	return 0;
	}
	
int main( )
	{
	// Math operators
	
	cout << "0 * one( ) = " << endl;
	cout << ( 0 * one( ) ) << endl << endl;
	
	// Logical operators
	
	cout << "1 || one( ) = " << endl;
	cout << ( 1 || one( ) ) << endl << endl;
	
	cout << "one( ) || one( ) = " << endl;
	cout << ( one( ) || one( ) ) << endl << endl;
	
	cout << "0 && one( ) = " << endl;
	cout << ( 0 && one( ) ) << endl << endl;
	
	cout << "zero( ) && one( ) = " << endl;
	cout << ( zero( ) && one( ) ) << endl << endl;
	
	// Bitwise operators
	
	cout << "1 | one( ) = " << endl;
	cout << ( 1 | one( ) ) << endl << endl;
	
	cout << "one( ) | one( ) = " << endl;
	cout << ( one( ) | one( ) ) << endl << endl;
	
	cout << "0 & one( ) = " << endl;
	cout << ( 0 & one( ) ) << endl << endl;
	
	cout << "zero( ) & one( ) = " << endl;
	cout << ( zero( ) & one( ) ) << endl;
	}
```

``` sh
g++ operations.cpp -o operations
operations
0*one( ) =
in one
0

1 || one( ) =
1

one( ) || one( ) =
in one
1

0 && one( ) =
0

zero( ) && one( ) =
in zero
0

1 | one( ) =
in one
1

one( ) | one( ) =
in one
in one
1

0 & one( ) =
in one
0

zero( ) & one( ) =
in zero
in one
0
8
```

## Review C++ memory model

An ***object*** is a piece of data in memory.
An object lives at an ***address*** in memory.
You can use an object during its ***lifetime***.
Lifetimes are managed according to ***storage duration***. Three options in C++:

1. Global (Static)
	* Lives for the whole program.
	* Managed by the compiler
2. Local (Automatic)
	* Lives during the execution of its local block.
	* Managed by you
3. Dynamic
	* You control the lifetime!

## Global variables

Defined:

1. Outside of a function/class definition,
2. As `static` within a function, or
3. As `static` within a class definition

Created before the program begins and live until the very end

Compiler reserves space for these

## Local variables

Lives inside a ***block { curly braces }***

Usually a function or loop body, but also just a plain block

Initialized when the declaration is run

Dies when the block is finished

## Dynamic variables

Called dynamic because:

1. Its size (or number) is determined at runtime.
2. When to create and destroy (or delete) it is determined at runtime.

The programmer decides ***how big*** a dynamic variable is, ***when it is created***, and ***when it is destroyed***

## new

Created dynamic variables using ***`new`***

This creates a new object in memory, and returns a pointer to that object.

``` cpp
int f1( )
	{
	int *p = new int;
	}

int f2( )
	{
	int *p = new int( 5 );
	}
```

In f1, the intial value is undefined

In f2, initializer syntax has been used to assign an intial value

## delete

Destroy dynamic variables using `delete`

Releases the claim on the space previously used by the `int`

Space can be recycled later by `new`

``` cpp
int *p = new int;

// Do something with p or the
// int that it points to.
:

delete p;
```

### Dangling pointers

To force an error, many programmers set the pointer to `nullptr` or `0` after delete

``` cpp
int *p = new int;

// Do something with p or the
// int that it points to.
:

delete p;
p = nullptr;
cout << *p;
```

### Double delete

You can only delete a dynamic variable once, and if you attempt to delete something that's already been deleted, you'll usually but not always crash.

#### Solution

Deleting a null pointer is ignored, so set the pointer to `nullptr`

``` cpp
int *p = new int;

// Do something with p or the
// int that it points to.
:

delete p;
p = nullptr;
delete p;
```

### bad delete

Ordinary objects can be destroyed by `delete`, *but only if they were created by `new`*

``` cpp
int i = 0;
int *p = &i;
delete p;
```

### memory leak

Dynamic variables live until the programmer destroys them using `delete`

This is true even if you "forget" the pointer to the object

``` cpp
int main( )
	{
	int *p1 = new int( 1 );
	int *p2 = new int( 2 );
	p1 = p2;
	}
```

There is no way to delete `p1`

Exiting the block causes the local objects `p1` and `p2` to vanish, but the dynamic object they point to remains.

Still have allocated dynamic objects but no means of reclaiming.

This is a memory leak.

### Why use dynamic variables?

1. If you want to manage the lifetime of the variable yourself
	* Not local, not global, but something in between
2. Many parts of the code refer to an object, but only want one copy in memory
	* Normaly, if many parts refer to the same object, those parts will be in different scopes

## Heap

A separate region of memory used for dynamic storage
Each object has its own lifetime, independent of the others around it. No Stack Frames!
The `new` and `delete` operators allow us to interact with the heap

* `malloc()` and `free()` are C’s new and delete
* The malloc and free functions also work with the heap, but they aren't generally used in C++ style programming.

A running program has an "address space", a collection of memory locations that are accessible to it

An address space is private to a running program - no other running program can access/modify it

### Shared memory

It is possible through calls to the operating system to be shared with another running program

## Program segments

There are typically five parts, called segments, in an address space

Address MAX

| |
|---|
|Stack<br>(grows down)|
|THE BIG VOID|
|Heap<br>(grows up)|
|Globals<br>(Fixed size)|
|Text<br>(The program)|

Address 0

### Text segment

The code comprising a compiled program goes in the text segment

It is at a very low address, but typically not at address zero

### Globals and heap segments

Immediately above the *text* section, the compilter allocates space for any global variables, and intializes them, when necessary

When dynamic variables are allocated with new, they come from the *heap*, which grows upwards

### Stack segment and the Big Void

When functions are called, stack frames are created on the *stack*, which grows downward

Since we don't know how big either of these will get, we keep a big hole in between the two called THE BIG VOID

### The little void

Most systems also reserve the first few thousand addresses starting at zero for another void

| |Global|Local|Dynamic|
|---|---|---|---|
|**Where in code?**|Outside function|Inside function (block) or args|Anywhere you sue `new`|
|**When created**|Beginning of program|Beginning of function (block)| You call `new`|
|**When destroyed**|End of program|End of function (block)| You call `delete`|
|**Size**|static|static|dynamic|
|**Location**|Globals|Stack|Heap|

### example

``` cpp
int main( )
	{
	int *p = new int( 100 ); // Always initialize variables
	assert( p );
	cout << *p << endl;
	delete p;
	p = nullptr;
	}
```

Local variable `int *p` goes on the stack

Dynamic memory `new int( 100 )` goes on the heap
Access a dynamic variable using a pointer

Use an `assert()` to catch null pointer before it causes a `SEGFUALT`

## NULL vs. `nullptr` vs. 0

### C style

``` cpp
// Preferred
int *ptr = NULL;

// Also works
int *ptr = 0;
```

### C++ style

``` cpp
// Preferred
int *ptr = nullptr;

// Also works
int *ptr = 0;

// Compile error.
// NULL is undefined.
int *ptr = NULL;
```

## Stack and heap exercises

### Can't delete something from stack

``` cpp
int i = 42,
	*p = &i;
delete p;
p = nullptr;
```

### How much memory is leaked?

``` cpp
int i = 4,
	*p = new int( 17 );
i = *p;
delete p;
p = nullptr;
```

No memory is leaked

### What's printed & how much memory is leaked?

``` cpp
int *p = new int( 100 ),
	*q = p;
delete q;
q = nullptr;
cout << *p << endl;
```

Printed value is undefined

No memory is leaked

### How much memory is leaked #3?

``` cpp
int *p = new int( 100 ),
	*q = new int( 42 );
q = p;
delete q;
q = nullptr;
```


Probably 4 bytes, `sizeof( int )` are leaked