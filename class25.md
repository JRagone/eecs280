Class 25
========

## Final exam review

### Final exam policies

1. Close book
2. Closed notes
3. One 8.5"x11" double-sided sheet
4. No calculators or electronics

### Topics

1. Container ADTs
2. Dynamic memory
3. Linked lists
4. Iterators
5. Function pointers and function objects
6. Recursion
7. Binary search trees
8. Exceptions

### Recall `UnorderedSet` class

We used a pointer to a dynamic array to store the set

``` cpp
class UnorderedSet
	{
		int *elements; // pointer to dynamic array
		int inUse; // current occupancy
		int capacity; // capacity of array
		static const int defaultCapacity= 100;
		void grow( ); // make dynamic array bigger
	
	public:
		~UnorderedSet( );
		UnorderedSet( int capacity = defaultCapacity );
		UnorderedSet( const UnorderedSet &other );
		UnorderedSet &operator=( const UnorderedSet &rhs );
		//...
	};
```

#### Helpful code

``` cpp
UnorderedSet::UnorderedSet( int capacity ) :
		inUse( 0 ), capacity( capacity )
	{
	elements = new int[ capacity ];
	}

UnorderedSet::~UnorderedSet( )
	{
	delete[ ] elements;
	}
	
void UnorderedSet::insert( int v )
	{
	if ( query( v ) )
		return;
	if( inUse == capacity )
		grow( );
	elements[ inUse++ ] = v;
	}

void UnorderedSet::grow( )
	{
	int *tmp = new int[ capacity + 1 ];
	for ( int i = 0; i < inUse; ++i )
		tmp[ i ] = elements[ i ];
	delete[ ] elements;
	elements = tmp;
	capacity += 1;
	}

UnorderedSet &UnorderedSet::operator=
		( const UnorderedSet &rhs )
	{
	if ( this == &rhs )
		return *this; //fix "s = s"
	delete[ ] elements; //remove all
	
	elements = new int[ rhs.capacity ];
	inUse = rhs.inUse;
	capacity = rhs.capacity;
	
	for ( int i = 0; i < rhs.inUse; ++i )
		elements[ i ] = rhs.elements[ i ];
	return *this;
	}
```

#### Draw stack and heap

``` cpp
int main( )
	{
	UnorderedSet is1( 1 );
	is1.insert( 42 );
	
	UnorderedSet is2( 3 );
	is2 = is1;
	is2.insert( 43 );
	}
```

### Iterators

``` cpp
class Gorilla
	{
	public:
		Gorilla( const string &name_in );
		~Gorilla( );
		string get_name( );
	};

int main( )
	{
	List<Gorilla*> zoo;
	zoo.push_front( new Gorilla( "Colo" ) );
	zoo.push_front( new Gorilla( "Koko" ) );
	
	//add code to print the name of each Gorilla
	
	//add code to delete each dynamic variable
	}
```

#### One solution

``` cpp
int main( )
	{
	List<Gorilla*> zoo;
	zoo.push_front( new Gorilla( "Colo" ) );
	zoo.push_front( new Gorilla( "Koko" ) );
	
	for ( List<Gorilla*>::Iterator it = zoo.begin( );
			it != zoo.end( ); ++it )
		{
		Gorilla *gorilla_ptr = *it;
		cout << gorilla_ptr->get_name( ) << endl;
		delete gorilla_ptr;
		}
	}
```

#### C++11 solution

``` cpp
int main( )
	{
	List<Gorilla*> zoo;
	zoo.push_front( new Gorilla( "Colo" ) );
	zoo.push_front( new Gorilla( "Koko" ) );
	
	// C++11 solution
	for ( auto gorilla_ptr:zoo )
		{
		cout << gorilla_ptr->get_name( ) << endl;
		delete gorilla_ptr;
		}
	}
```

### Function pointers

Your code lives in Text, and you can get a pointer to it

Function pointers are variables

Function pointers are like a second name for a function

Function pointers let us decide between two ( or more ) functions at run time

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
	fpointer = IsOdd;
	cout << fpointer( 42 ); //false
	}
```

**`fpointer`** is a variable

**`( *fpointer )( )`** is a pointer to a function

**`bool ( *fpointer )( )`** returns a bool

**`bool ( *fpointer )( int )`** takes a single integer argument

``` cpp
int main( )
	{
	bool ( *fpointer )( int );
	fpointer = &IsOdd;
	fpointer = IsOdd;
	cout << fpointer( 42 ); //false
	}
```

Assign a value, the address of a function

The & address-of operator is optional

The instructions for executing a function are stored somewhere. A function pointer points to that address

#### Calling function pointers

Use our `fpointer` variable just like a function

It will do the same thing as `IsOdd` in this example

Dereference is optional

``` cpp
int main( )
	{
	bool ( *fpointer )( int );
	fpointer = IsOdd;
	cout << ( *fpointer )( 42 );
		// false, "*" is optional
	cout << fpointer( 42 );
		// false
	}
```

#### Rewriting AnyOdd and AnyEven

Now we'll add an additional parameter to `AnyOf` using our new type

``` cpp
bool AnyOf( const List<int> &l,
		bool ( *fpointer )( int ) );
```

Use `fpointer` instead of `IsOdd` or `IsEven`

``` cpp
bool AnyOf( const List<int> &l,
		bool ( *fpointer )( int ) )
	{
	for( auto i:l )
		if ( fpointer( i ) )
			return true;
	return false;
	}
```

We can write `AnyOdd` or `AnyEven`

``` cpp
bool AnyEven( const List<int> &l )
	{
	return AnyOf( l, IsEven );
	}

bool AnyOdd( const List<int> &l )
	{
	return AnyOf( l, IsOdd );
	}
```

#### Why function pointers?

* Use a simple function, like `IsOdd` or `IsEven` to change the behavior of a more complicated function, like `AnyOf`
* Often, a more complicated function *that somebody else wrote*
	* STL has many algorithms, `#include <algorithm>`

### Using `std::algorithm`

``` cpp
#include <iostream>
#include <list>
#include <algorithm>
using namespace std;

bool IsOdd( int i ) { return ( i % 2 ) != 0; }
bool IsEven( int i ) { return ( i % 2 ) == 0; }

int main( )
	{
	list<int> il = {1,3,5};
	cout << AnyOf( il.begin( ), il.end( ), IsOdd ); //1
	cout << AnyOf( il.begin( ), il.end( ), IsEven );//0;
	}
```

### Predicates

A *predicate* is used to control an algorithm

Input: One element

Output: `bool`

We can wirte a simple predicate to control a complicated algorithm that somebody else wrote

#### Exercise

How would you use `AnyOf` to see if `list` has any elements greater than 2?

How would you use it to see if a list has any elements greater than 42?

#### Solution

Write new function pointers!

Ought to be able to generalize more by writing a single fpointer that is `GreaterN` and use that single fpointer no matter what the larger than target is

Should not have to know the comparison value at compile time

#### Problem

Unfortunately, solving these problems of creating a `greaterN` function with function pointers requires an unsightly global variable

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

We need to create functions at run time

Given an integer `limit`, return a fpointer that takes one integer argument, `N`, and returns true if `N > limit`

Why can't we create functions at run time?

Because function are not *first class objects*

### First-class objects

There are a lot of things we can do with integers in a running program, including:

1. Create them
2. Destroy them
3. Pass them as arguments
4. Return them as values

For any entity in a programming language, we say that the entity is *first-class* if you can do all four of these things

Functions are not first class objects in C/C++, because we cannot create them at runtime

Classes are first class objects in C/C++

Solution is to make a class that pretends to be a function: **functor**

### Function call operator

We can redefine the function call operator on a class `GreaterN`

``` cpp
class GreaterN
	{
		int limit;
	public:
		GreaterN( int limit_in ) :
				limit( limit_in )
			{
			}
		bool operator( ) ( int n )
			{
			return n > limit;
			}
	};
```

Unlike functions with function pointers, objects can have per-object state, which allows us to specialize on a per-object basis

### Using function objects

Constructors create new variables

Function call operators work with existing variables

We can use this new class to "generate" specialized functors

### Function objects as function inputs

Now we have covered how to create function objects

In order to complete the `AnyOf` function, we need to pass a functor as a function input

We did this before using function pointers

Problem: since a functor is a custom type, each functor will have its own type

Solution: generalize the type using templates

### Templated functions

We want to use the `AnyOf` function with any functor `fpointer`

``` cpp
template <typename fpointer>
bool AnyOf( const List<int>
		&l, fpointer pred );
	for( auto i:l )
		if ( pred( i ) )
			return true;
	return false;
```

``` cpp
List<int> l; // fill with ( 1 2 3 )
GreaterN g2( 2 );
GreaterN g6( 6 );
cout << AnyOf( l, g2 ) << endl;
cout << AnyOf( l, g6 ) << endl;
```

We can even get limits from the user

``` cpp
List<int> l; // fill l

// ask user for limits
int less_limit=0, greater_limit=0;
cin >> less_limit >> greater_limit;

// create functors
LessN less( less_limit );
GreaterN greater( greater_limit );

// use functors
cout << AnyOf( l, less ) << endl;
cout << AnyOf( l, greater ) << endl;
```

***The ability of objects to carry per-object state plus overrriding the "function call" operator gives us the equivalent of a "function factory"***

### Comparision functors

* So far, we have written *precedents* using functors
	* A *functor* is used to **control an algorithm**. The input is one element, the output is a `bool`

* Another use for functors is a *comparison* operator
	* A *comparision* is used to **define order**. The inputs are two elements of the same type, the output is `bool`

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

### Comparision fucntors

The following comparison functor compares two `Animal` objects by name and returns true if name of `a` is before `b` in alphabetical order

``` cpp
class AnimalLess
	{
	public:
		bool operator( )( const Animal &a,
				const Animal &b ) const
			{
			return a.get_name( ) < b.get_name( );
			}
	};

AnimalLess less;
Animal fergie( "Fergie" );
Animal myrtleII( "Myrtle II" );
cout << less( fergie, myrtleII ) << endl;
```

#### Exercise

Use `std::max_element` to print the name of the max chicken

``` cpp
// Returns an iterator pointing to the element
// with the largest value in the range first
// to last.

template <typename Iterator, typename Compare>
	bool max_element ( Iterator first,
		Iterator last, Compare comp );

int main( )
	{
	vector<Animal> chickens;
	chickens.push_back( Animal( "Myrtle" ) );
	chickens.push_back( Animal( "Myrtle II" ) );
	chickens.push_back( Animal( "Magda" ) );
	chickens.push_back( Animal( "Fergie" ) );
	
	//Solution
	auto max_chicken = max_element(
		chickens.begin( ), chickens.end( ),
		AnimalLess( ) );
	cout << max_chicken->get_name( ) << endl;
	}
```

### Functors

Write a functor called `InRange` that ruetns `true` if an input integer is within an inclusive range: `[min,max]`

``` cpp
class InRange
	{
		int min, max;
	public:
		InRange( int min_in, int max_in )
			: min( min_in ), max( max_in )
		{ }
	
	bool operator( ) ( int n )
		{
		return ( min <= n ) &&
			( n <= max );
		}
	};
```

Write a `main( )` function that uses instances of `LessN`, `GreaterN` and `InRange` to check if water is a solid, liquid or gas at a given temperature

``` cpp
int main( )
	{
	cout << “Enter temp\n“;
	int temp=0;
	cin >> temp;
	LessN is_solid( 32 );
	InRange is_liquid( 32, 212 );
	GreaterN is_gas( 212 );
	cout << is_solid( temp ) << endl;
	cout << is_liquid( temp ) << endl;
	cout << is_gas( temp ) << endl;
	}
```

### Recursion

#### Recursive `tree_height`

``` cpp
struct Node
	{
	int datum;
	Node *left;
	Node *right;
	};

int tree_height( Node *n )
	{
	// BASE CASE – Empty tree has size 0
	if ( n == nullptr )
		return 0;
		
	// RECURSIVE CASE
	int left_height = tree_height( n->left );
	int right_height = tree_height( n->right );
	return 1 + std::max( left_height,
		right_height );
	}
```

``` cpp
int tree_height( Node *n )
	{
	// Alternative solution
	if ( !n )
		return 0;
	return 1 + std::max(
		tree_height( n->left ),
		tree_height( n->right ) );
	}
```

### Exceptions

***Exceptions*** let us detect an error in one part of the program and correct it in a different part of the program

For example, we could detect an error in `factorial( )` and correct it in `main( )`

#### Exception propagation

When an exception occurs, it ***propagates*** from a function to its caller until it reaches a handler

You can imagine exceptions as a multi-level return

#### Exception handling in C++

1. When coe detects an error, it uses a **throw** statement
2. Code that might cause an error goes in a `try{}` block
3. Code that corrects an error goes in a `catch{}` block
4. If there is no code that corrects the error, the program exits

##### Terminology

1. You ***throw*** or ***raise*** an exception
2. You use a ***catch block***, also called an ***exception handler***, to deal with it

When code detects an error, it uses a `throw` statement

***Exceptions have types and values*** just like variables

You can think of this value as being a kind of paremeter to the exception

``` cpp
int n = 0;
throw n;

char c = 'e';
throw c;
```

#### Exception example

If `n` is negative:

1. No more code from this function executes
2. Control passes "up the chain" to the caller

``` cpp
//EFFECTS: returns n!, throws n
//for negative inputs

int factorial ( int n )
	{
	if ( n < 0 )
		throw n;
	int result = 1;
	while ( n > 0 )
		{
		result *= n;
		n -= 1;
		}
	return result;
	}

int main( )
	{
	string f; //function
	int n; //number
	while ( cin >> f >> n )
		try
		{
		if ( f == "factorial" )
			cout << factorial( n ) << endl;
		}
		catch( int i )
			{
			cout << "try again" << endl;
			}
	}
```

***The curly braces are required for try and catch***

#### Another exception example

``` cpp
int combination( int n,
		int k )
	{
	return factorial( n ) /
		( factorial( k ) *
			factorial( n – k );
	}
	
int main( )
	{
	string f; //function
	int n; //number
	while ( cin >> f >> n )
		try
			{
			if ( f == "combination" )
			cout << combination( n ) <<
			endl;
			}
		catch( int i )
			{
			cout << "try again" << endl;
			}
		}
	}
```

#### Unhandled exceptions

When an exception is not caught by a catch block, it propagates all the way to the caller of `main( )`, and the program exits

#### Type discrimination

A `try{}` block can have multiple `catch{}` blocks to handle different exception types, but they must all be one right after the other

``` cpp
try
	{
	if ( foo )
		throw 4;
	// some statements go here
	if ( bar )
		throw 2.0;
	// more statements go here
	if ( baz )
		throw 'a';
	}
catch ( int n )
	{ }
catch ( double d )
	{ }
catch ( char c )
	{ }
catch ( ... )
	{ }
```

The last handler is a ***default handler***, which matches any exception type. It can be used as a "catch-all" in case no other catch block matches

The "..." means ***match anything***

#### Exception types

Code often uses custom types to describe errors

We can use the class mechanism to declare custom types

``` cpp
class NegativeError
	{
	//...
	};

class InputError
	{
	//...
	};

//EFFECTS: returns n!, throws
//NegativeError for n < 0
int factorial ( int n )
	{
	if ( n < 0 )
		throw NegativeError( );
	int result = 1;
	while ( n > 0 )
		{
		result *= n;
		n -= 1;
		}
	return result;
	}

int main( )
	{
	//...
	while ( cin >> f >> n )
		{
		try
			{
			//...
			}
		catch ( NegativeError n )
			{
			cout << "try a positive number“
				<< endl;
			}
		catch ( ... )
			{
			cout << "try again" <<endl;
			}
		}
	}
```

#### Cannot catch hardware exceptions

Cannot `catch` anything not actually `thrown`, as is the case when dereferencing a null pointer

