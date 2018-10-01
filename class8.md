Class 8
=======

## Classes

|struct|class|
|---|---|
|Heterogeneous aggregate data type|Heterogeneous aggregate data type|
|C style|C++ style|
|Contains only data|Contains data and functions|
|Undefined by default|Constructors can be used to initialize|
|All data is accessible|Control of data access|

### Members of class

A class contains both data *and* functions. These are called ***member data*** and ***member functions***, accessed via a ***this*** pointer to this particular instance.

We usually dispense with the ***this*** pointer. It's as if all of the members of the instance of the class are local variables.

``` cpp
class Triangle
	{
	public:
		double A, B, C; //edge lengths
		double Area( )
			{ //compute area
			double s = ( A + B + C )/2;
			return sqrt( s * ( s - A )
						   * ( s - B )
						   * ( s - C ) );
			}
	}
```

***public*** means members are accessible from outside the class just like members of a struct

In C, the area calculation had to be done outside the struct ADT, which could only hold data, not functions

In C++, it's written as a ***member*** of the class ADT, which can hold data and functions

To pass ADTs in C, we had to pass a pointer

In C++, a ***this*** pointer variable is automatically passed by the compiler

Inside of the class Triangle, either of these do the same thing.

``` cpp
this->A

A
```

Access member functions using the ***dot operator***, just like member variables

``` cpp
int main( )
	{
	Triangle t;
	t.A = 3; t.B = 4; t.C = 5;
	cout << "area = " << t.Area( ) << endl;
	}
```

### Classes in memory

We get storage for each data member inside the class, just like a struct, but not the functions

The functions are shared among all instances of that class

### Value semantics

We can copy a class object

``` cpp
int main( )
	{
	Triangle t;
	t.A = 3; t.B = 4; t.C = 5;
	Triangle t2 = t1;
	}
```

### Arrays of classes

Just like a struct, we can create arrays of class objects

``` cpp
int main( )
	{
	const int Size = 3;
	Triangle triangles[ Size ];
	for ( Triangle *t = triangles; t < triangles + SIZE; ++t )
		cout << "area = " << t->Area( ) << endl;
	
	} 
```

### Initialization problem

Just like a struct, values are undefined for a new class object

Uninitialized values are a common source of bugs

``` cpp
int main( )
	{
	Triangle t;
	// forget to initialize
	t.Area(); //garbage!
	}
```

### Constructors

*member function that has the same name as the class*

A constructor ***runs automatically*** when a new object is created. It is typically used to initialize member variables.

``` cpp
class Triangle
	{
	public:
		double A, B, C;
		double Area( )
			{
			:
			};
		Triangle( ) //Same name as the class, no inputs
			{
			A = B = C = 0; // Initializes members
			} // No return value
	};

int main( )
	{
	Triangle t; //Constructor runs automaticallys
	t.Area( );
	}
```

### Function overloading

*two different functions with the same name, but different prototypes*

Since the compiler knows the argument types, it can select the correct constructor when a new object is created

``` cpp
class Triangle
{
	public:
		double A, B, C;
		:
		Triangle( )
			{
			A = B = C = 0;
			}
		Triangle( double a, double b, double c )
			{
			A = a; B = b; C = c;
			}
};

int main( )
	{
	Triangle t = Triangle( 3, 4, 5 );
	cout << "area = " << t.Area( ) << endl;
	}
```

### Two versions of instantiation syntax

Either of the following lines do the same thing

``` cpp
Triangle t = Triangle( 3, 4, 5 );

Triangle t( 3, 4, 5 );
```

### Initializer lists

This code:

``` cpp
class Triangle
	{
	public:
		:
		Triangle( ): a( 0 ), b( 0 ), c( 0 )
			{
			}
	};
```

works the same way as this:

``` cpp
Triangle( )
	{
	a=0; b=0; c=0;
	}
```

One way may be more efficient, depending on the compiler

The second constructor also uses an initializer list

``` cpp
class Triangle
	{
	public:
	:
	Triangle( double a, double b, double c ) : A( a ), B( b ), C( c )
	{
	}
};
```

#### Pitfall

The order in which elements are initialized is the order they appear in the object, NOT the order in the initialization list

### Private members

Private members can be accessed only by member functions

Non-members can only access public members

``` cpp
class Triangle
	{
	private:
		double A, B, C;
	public:
		double Area( )
			{
			double s = ( A + B + C ) / 2;
			return sqrt(s*( s - A ) * ( s – B ) * ( s - C ) );
			}
		Triangle( ) { A = 0; B = 0; C = 0; }
		Triangle( double a, double b, double c )
			{ A = a; B = b; C = c; }
	};

int main( )
	{
	Triangle t( 3, 4, 5 );
	t.c = 9; //compiler error
	cout << "area = " << t.area( ) << endl;
	}
```

* By default, ever member of a class is private
* A `private` member is visible only to other members of this class
* The `public` keyword is used to signify that everything after is is visible to anyone who sees the class declaration, not just members of this class
* Usually, we make member variables `private`
* `public` member variables often indicate a bad design

### `get` and `set` functions

For convenience, some classes include functions to get and set member variables

#### `get`

*`public` function that returns a copy of a `private` member variable*

``` cpp
class Triangle
	{
	public:
		//EFFECTS: returns edge A, B, C;
		double GetA( ) { return A; }
		double GetB( ) { return B; }
		double GetC( ) { return C; }
	};
```

#### `set`

*`public` functions that modifies a `private` member variable*

``` cpp
class Triangle
	{
	public:
		//REQUIRES: A, B, C are non-
		// negative and form a triangle.
		//MODIFIES: A, B, C
		//EFFECTS: sets lengths of edges.
		void SetA( double a ) { a = a; }
		void SetB( double b ) { b = b; }
		void SetC( double b ) { c = c; }
	};
```

`set` functions allow you to run extra code when a member variable changes

### const member functions

``` cpp
class Triangle {
	//...
	double area() const;
};
```

This is a new use of `const`, and it means "this member funciton promises not to modify any member variable"

#### Review

We have now seen three uses of `const`:

``` cpp
const int *p;			//the pointed-to object cannot change
int *const p;			//the pointer cannot change
void area() const;	//member function cannot change
						//member variables
```

## Data abstraction

1. **Data abstraction** lets us separate ***what*** a type is (and what it can do) from ***how*** the type is implemented.
2. In C++-style code, use a ***class*** to implement data abstraction as an ***Abstract Data Type*** (ADT).
3. ADTs let us model complex phenomena with more complex types than simply ints, doubles, etc.
4. ADTs make programs easier to maintain and modify because you can change the implementation and users can’t tell.

### Information Hiding

*Protect and hide our code form other code that uses it*

### Encapsulation

*Keeping data and relevant functions together*

### Creating our ADT

Just like C-style ADTs, we can use multiple files to organize our code

#### `Triangle.h` file

We write an abstract ***description*** of values and operations: ***What*** the data type does, but ***not how***

#### `Triangle.cpp` file

We ***implement*** the ADT: ***How*** the data type works

``` cpp
#include "Triangle.h"
#include <cmath>
using namespace std;
```

`#include Triangle.h` tells the compiler to "copy-paste" the `Triangle.h` header file at the top of this file

#### Scope resolution operator

``` cpp
Triangle::Triangle( )
	: a( 0 ), b( 0 ), c( 0 ) { }

Triangle::Triangle( double a_in, double b_in, double c_in )
	: a( a_in ), b( b_in ), c( c_in ) { }
```

`::` is the ***scope resolution operator***, which tells the compiler that this function is inside the scope of the `Triangle` class

It is needed so that the compiler knows that this is a ***member*** function inside `Triangle`

#### `Graphics.cpp` file

Finally we ***use*** our new ADT

### Representation invariant

*description of how member variables should behave*

``` cpp
class Triangle
	{
	...
	// edges are non-negative and form a triangle
	double a, b, c;
	};
```

* Member variables are a class's representation
* Representation invariants are rules that the representation must obey immediately before and immediately after any member function execution
* You can check invariants with **assert( )**

### Testing C++ style ADTs

``` cpp
int main( )
	{
	test_triangle_basic( );
	}

void test_triangle_basic( )
	{
	Triangle t( 3, 4, 5 );
	assert( t.area( ) == 6 );
	assert( t.get_a( ) == 3 );
	t.set_a( 4 );
	assert( t.get_a( ) == 4 );
	cout << "Triangle Basic Test Passed\n";
	}
```