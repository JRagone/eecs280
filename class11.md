Class 11
========

## Information hiding and encapsulation

### Information Hiding

*Protect and hide our code from other code that uses it*

### Encapsulation

*Keeping data and relevant functions together*

## Container

*Holds other objects*

### Example

An array can hold integers

``` cpp
int array[ 10 ];
array[ 4 ] = 517;
```

This is a "set" in the mathematical sense: a collection of zero or more integers, with no duplicates

## `Set`

### `Set` abstraction

Let's create an abstraction that holds a set of integers

There are four operations on this set that we will define:

1. Insert a value into the set
2. Remove a value from the set
3. Determine if a value is a member of the set
4. Count the number of elements in the set

### `Set` representation

Choosing a representation involves two things:

1. Deciding what concrete data elements will be used to represent the values of the set
2. Providing an implementation for each method

### Static member variables

**static** means "there's only one"

All instances of the class share this member variable

``` cpp
static int add( int a, int b )
	{
	return a + b;
	}

class Set
	{
	public:
		static const int
			NumberAllocated = 100;
	};
```

### Static function

*Visible only inside one file (internal linkage)*

### Static member variable

*All instances of the `Set` class share this member variable*

### Searching `Set`

``` cpp
class Set
	{
	public:
		//...
		static const int NumberAllocated = 100;
	
	private:
		int elements[ NumberAllocated ];
		int numberOfElements;
		int Find( int v ) const;
	};

int Set::Find( int v ) const
	{
	for ( int i = 0; i < numberOfElements; ++i )
		if ( elements[ i ] == v )
			return i;
	return NumberAllocated;
	}
```

### `Set::IsMember( )`

``` cpp
bool Set::IsMember( int v ) const
	{
	return Find( v ) != NumberAllocated;
	}
```

### `Set::Insert( )`

``` cpp
void Set::Insert( int v )
	{
	if (IsMember( v ) )
		return;
	assert( numberOfElements < NumberAllocated ); //REQUIRES!
	elements[ numberOfElements++ ] = v;
	}
```

The assert keeps the program from running off the end of the array

### `Set::Remove( )`

``` cpp
void Set::Remove( int v )
	{
	int victim = Find( v );
	if ( victim == NumberAllocated )
		return; //not found
	elements[ victim ] = elements[ --numberOfElements ];
	}
```

### `Set` Initialization

``` cpp
Set::Set( ) : numberOfElements( 0 )
	{
	}
```

Intialization list is called before entering the braces

### `Set::Print( )`

``` cpp
void Set::Print( ) const // The print function inside the Set class
	{
	cout << "{ ";
	for ( int i = 0; i < numberOfElements; ++i )
		cout << elements[ i ] << " ";
	cout << "}"<< endl;
	}
```

* How to use `Set` with different datatypes?

### Generic programming

*Write the algorithm,* ***specify the type later***

C++ implements generic programming using the **template** mechanism

We used templates with vectors in project 1

#### Template notation

***T is the type contained by this `Set`. It's like a variable name, and some programmers like to use value_type instead of T***

``` cpp
template < typename T >
	class Set
		{
		// ...
		};

template < class T >
	class Set
		{
		// ...
		};
```

##### Declare `Set` using `T` instead of `int`

``` cpp
template < typename T >
	class Set
		{
		public:
			Set( );
			void Insert( T v );
			void Remove( T v );
			bool IsMember( T v ) const;
			int Count( ) const;
			static const int NumberAllocated = 100;
		
		private:
			T elements[ NumberAllocated ];
			int numberOfElements;
			int Find( T v ) const;
		};
```

##### Define member functions using `T` instead of `int`

``` cpp
template < typename T >
	bool Set< T >::IsMember( T v ) const
		{
		return (Find( v ) != NumberAllocated);
		}
```

Each time the compiler sees a different type `T`, it copies the class, and substitutes the type for `T`

``` cpp
int main( )
	{
	Set< int > is1;
	//compiler compiles Set with T=int
	
	Set< string > chickens;
	//compiler compiles Set with T=string
	
	Set< int > is2;
	//compiler reuses Set compiled with
	// T=int
	}
```

##### Template code goes in the `.h` file

The compiler needs to see the declarations (prototypes) and definitions (implementations) of `Set` to compile a template

This means we need to put both the class declaration (prototype) *and* definition (implementation) in `Set.h` file


#### Include guards

We use *include guards* to protect against redefintion and ensure that `Set` code only appears once

Two ways to use *include guards*

``` cpp
#ifndef SET_H
#define SET_H
//Set.h

template <typename T>
	class Set
		{
		//...
		};

//implementation ...
#endif
```

``` cpp
#pragma once
//Set.h

template <typename T>
	class Set
		{
		//...
		};

//implementation ...
```

* Even with templates, the containers are still *homogenous*, because one container variable can hold only one type

### Static polymorphism

``` cpp
int main( )
	{
	Set< int > is;
	//...
	Set< string > chickens;
	//...
	}
```

This is an example of ***static polymorphism***

***static***: at compile time

***polymorphism***: different behavior for different types