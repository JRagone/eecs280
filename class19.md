Class 19
========

## Function objects

### `AnyOdd( )`

``` cpp
bool IsOdd( int i )
	{
	return i % 2 != 0;
	}

// EFFECTS: returns true if il has any odd
// element, false otherwise
bool AnyOdd( const List<int> &list )
	{
	for( List<int>::Iterator i = list.begin( );
		i != list.end( ); ++i )
		if ( IsOdd( *i ) )
			return true;
	return false; // No odd numbers found.
	}
```

or

``` cpp
bool IsOdd( int i )
	{
	return i % 2 != 0;
	}
	
// EFFECTS: returns true if l has any odd
// element, false otherwise
bool AnyOdd( const List<int> &list )
	{
	for( auto i:list )
		if ( IsOdd( i ) )
			return true;
	return false; // No odd numbers found.
	}
```

### `AnyEven( )`

``` cpp
bool IsEven( int i )
	{
	return i % 2 == 0;
	}
	
bool AnyEven( const List<int> &list )
	{
	for( auto i:list )
		if ( IsEven( i ) )
			return true;
	return false;
	}
```

We've copy pasted our code for `AnyOdd( )` to write `AnyEven( )`

Let's make a new function that can do the work of both

### Function pointers

*Function pointers are variables*

*Function pointers are like a second name for a function*

*Function pointers let us decide between two ( or more ) functions at run time*

``` cpp
bool IsOdd( int i )
	{
	return i % 2 != 0;
	}
	
bool IsEven( int i )
	{
	return i % 2 == 0;
	}
	
int main( )
	{
	bool ( *fpointer )( int );
	fpointer = &IsOdd;
	fpointer = IsOdd;
	cout << fpointer( 42 ); //false
	}
```

The `&` address-of operator is optional

The instructions for executing a funciton are stored somewhere. A function pointer points to that address

### `AnyOf( )`

We can use function pointers to make a funciton behave like a variable

``` cpp
bool AnyOf( const List<int> &list,
		bool ( *fpointer )( int ) )
	{
	for( auto i:list )
		if ( fpointer( i ) )
			return true;
	return false;
	}
	
bool AnyEven( const List<int> &list )
	{
	return AnyOf( list, IsEven );
	}
	
bool AnyOdd( const List<int> &list )
	{
	return AnyOf( list, IsOdd );
	}
```

### Predicates

A *predicate* is used to control an alorithm

* Input: one element
* Output: `bool`

We can write a simple predicate to control a complicated algorithm that somebody else wrote

### Exercise

How would we use `AnyOf` to see if `list` has other properties?

### Solution

Write new function pointers!

``` cpp
bool greater2( int n )
	{
	return ( n > 2 );
	}

bool greater42( int n )
	{
	return ( n > 42 );
	}
```

We should generalize these to functions to `GreaterN( )` and use that single fpointer no matter what the larger target is

### Problem

Unfortunately, creating a `greaterN` function with function pointers requires a global variable

``` cpp
int Limit; // Global variable

bool greaterN( int n )
	{
	return n > Limit;
	}

List<int> list; // fill list
cin >> Limit; // get limit
cout << AnyOf( list, greaterN );
```

### Creating functions at run time

* We need to create functions at run time
* Given an integer `limit`, return a fpointer that takes one integer argument, `N`, and returns true if `N > limit`
* Functions cannot be created at run time because they are not *first class objects*

### First-class objects

A first class object, such as an `int` can let us do all four things at run time:

* Create them
* Destroy them
* Pass them as arguments
* Return them as values

Functions are not first class objects in C/C++

Classes are first class objects in C/C++

Next, we'll make a class that pretends to be a function

* Overload the *function call operator*
* This is a *function object* or *functor*

### Function call operator

We can redefine almost any operator for a class, and the "function-call" is just another operator

``` cpp
class Greater2
	{
	public:
		bool operator( ) ( int n )
			{
			return n > 2;
			}
	};
```

The public method overloads the "function-call" operator on instances of `Greater2` - the mehtod takes a single integer argument, and returns a Boolean result

### Using function objects

``` cpp
Greater2 g2;
cout << g2( 4 ) << endl;
```

``` sh
./a.out
1
```

The class `Greater2` has defined the "function-call operator", and so that member function runs when we use the syntax `g2( 4 )`, passing the argument 4

### Implementing function objects

So far, this is a lot like function pointers

However, unlike functions with funciton pointers, objects can have per-object state, which allows us to specialize on a per-object basis

``` cpp
class GreaterN
	{
		int limit;
	public:
		GreaterN( int limit ) :
				limit( limit )
			{
			}
		bool operator( ) ( int n )
			{
			return n > limit;
			}
	};
```

When we create instances of `GreaterN`, we store the `limit` in a member variable, and later can test inputs against that `limit`

We intialize a `GreaterN` object with a `limit` using a constructor

### Using function objects

COnstructors and function call operators are easy to mix up

``` cpp
GreaterN g2( 2 ); //ctor
GreaterN g6( 6 ); //ctor

cout << g2( 4 ) << endl; //function call operator
cout << g6( 4 ) << endl; //function call operator
```

### Function objects as funciton inpus

We need to pass a functor as a function input to complete `AnyOf( )`

Problem: since a functor is a custom type, each functor will have its own type

Solution: generalize the type using templates

### Templated functions review

``` cpp
template <typename T>
T sum( T a[ ], int size );

double a[ 100 ] = {... }
int b[ 200 ] = {... }

double aSum = sum( a, 100 );
int bSum = sum( b, 200 );
```

### Templated functions

``` cpp
template <typename T>
T sum( T a[ ], int size );

template <typename fpointer>
bool AnyOf( const List<int>
		&list, fpointer pred );
```

### Using function objects again

``` cpp
List<int> list; // fill list

cout << AnyOf( list, GreaterN( 2 ) ) << endl;

cout << AnyOf( list, LessN( 2 ) ) << endl;

cout << AnyOf( list, InRange( 32, 212 ) ) << endl;
```

The ability of objects to carry per-object state **plus** overriding the "function call" operator gives us the equivalent of a "function factory"

### Comparison functors

* So far, we have written *precedents* using functors
	* A *functor* is used to **control an algorithm**. The input is one element, the output is a `bool`

* Another use for functors is a *comparison* operator
	* A *comparison* is used to **define order**. The inputs are two elements of the same type, the output is a `bool`

### Precedent vs. comparison functors

``` cpp
class Myfpointer
	{
	public:
		bool operator( )( T input )
			{ /* ... */ }
	};

class MyComparison
	{
	public:
		bool operator( )( T a, T b )
			{ /* ... */ }
	};
```