Class 4
=======

## Address-of vs. reference type

``` cpp
int swap ( int &a, int &b )
	{
	int x,
		*p = &x, //Hidden
		&r = x;
	:
	r = 5;
	*p = 5;
	:
	}
```

`&` can mean either the ***address-of*** operator or that you are declaring a variable as a ***reference type***

`x` is an ***int***

`p` is a ***pointer to an int***, initialized to the ***address of x***

`a`, `b`, and `r` are ***references to ints***

`a` and `b` refer to objects passed by the caller

`r` is set to refer ***permanently*** to `x`

`x`, `*p`, and `r` all refer to the ***same thing***

When you define a reference variable `r`, the compiler instead generates ***a hidden definition for p***

Any reference to `r` is replaced by a dereference of the hidden pointer `p`

`a` and `b` are passed as hidden pointers, dereferenced anytime `a` or `b` is accessed

* When you declare a reference variable, the compiler ***behaves*** as if you created a pointer to the the object you're referencing, so whenever you make a change, it grabs the pointer and makes the change.

## Pointers

``` cpp
int x = 7;
cout << &x; //0x804240c0
int *p = &x;
```

* A pointer is a type of object whose ***value*** is the ***address*** of an object
* To declare a pointer variable, affix a `*` to the left of the the name `int *ptr = //...`
* There is a separate pointer type for each kind of thing you could point to, and you can't mix them

``` cpp
int x = 7;
int *p = &x;
cout << *p; //7
```

To get the object a pointer points to, use the `*` operator, called ***dereference***

### Dereference vs. pointer type

Don't confuse the ***dereference*** operator with a ***pointer type***

#### Examples

``` cpp
int x = 7;	// x is a variable whose type is int

int *p = &x; 	// p is a variable whose type is
		// pointer-to-int
				
cout << *p;	// The dereference operator retrieves the
		// object it points to. Prints 7.
```

``` cpp
int foo = `;
int *bar = &foo;
foo = 2;
*bar = 3;

cout << foo;	//3
cout << bar;	//0x...
cout << *bar;	//3
```

``` cpp
int *x, *y;	//Allocate but don't intialize pointers
int a = -1;

x = &a;
cout << *x;	//-1
*x = 42;
cout << a;	//42
*y = 13;	//Undefined! y has not been initialized
cout << *y;	//y=<garbage>
y = x;
cout << *y;	//*y=42
cout << a;	//a=42
```

### What can you do with pointers?

1. Pointers let us work with objects *indirectly*
2. Work with *arrays* of objects
3. Pointers are useful in C-programming

## C++ objects

### Atomic

* Also known as ***primitive***
* `int`, `double`, `char`, etc.
* Pointer types

### Arrays (homogeneous)

*A* ***contiguous*** *sequence of objects of the same type*

* Have a fixed size
* Hold elements of all the same type
* Have ordered elements
* Occupy a ***contiguous*** chunk of memory
* Support constant time random access by ***indexing***

The value of an array variable is the ***address of the first element***

Since an arrray is contiguous, the address of the first element is all you need

``` cpp
int array_max( int *a )		// Might as well pass it as an int *
	{
	cout << a; //0x804240c0
	//...
	}

int main( )
	{
	int array [ 5 ] = { 1,2,3,4,5 }
	array_max( array );	// Turns into a pointer before bieng passed
	}
```

Passing an array to function turns it into a pointer

### Compound (heterogeneous)

*A compound object made up of member sub-objects*

* The members and their types are defined by a ***class*** or a ***struct***

### Type sizes

C++ Standard only requires:
1. A `char` is always one byte
2. A `short` is always at least as big as a `char`
3. And `int` is always at least as big as a `short`
4. A `long` is always at least as big as an `int`
