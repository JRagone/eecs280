Class 9
=======

## Classes

* In C++, structs can have functions and constructors
* structs can also inherit from other structs
* public and private keywords may be used
* Default is public for members and inheritance
* Only place where a struct cannot be used is where a class can be in an argument to a template

### Member functions

* In C++, a ***this*** pointer variable is automatically passed by the compiler

* The ***this*** pointer is optional

``` cpp
double Area( )
	{
	double s = ( this->A + this->B +
					this->C )/2;
	return sqrt(s * ( s - this->A )
					 * ( s - this->B )
					 * ( s - this->C ) );
	}
double Area( )
	{
	double s = ( A + B + C )/2;
	return sqrt(s * ( s - A )
					* ( s - B )
					* ( s - C ) );
	}
```

* Call member functions using the ***dot operator***

``` cpp
t.Area()
```

### Constructors

*member function that has the **same name as the class***

A constructor ***runs automatically*** when a new object is created

A constructor is typically used ot intialize member variables

``` cpp
class Triangle
	{
	public:
		double A, B, C;
		double Area( )
			{
			:
			};
		Triangle( )
			{
			A = B = C = 0;
			}
	};
```

#### Initializer lists

``` cpp
class Triangle
	{
	public:
		double A, B, C;
		:
		Triangle( ): A( 0 ), B( 0 ), C( 0 )
			{
			}
};
```

### Function overloading

*two different functions with the same, but different signatures*

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
		Triangle( double a, double b,
					double c )
			{
			A = a; B = b; C = c;
			}
	};
```

### Order of execution

Base class constructors run even without a constructor for the derived class

Constructors run in the order the members appear in the class

#### Example

``` cpp
#include <iostream>
using namespace std;

class Base2
	{
	public:
		int i;
		Base2( int i ) : i( i )
			{
			cout << “i = " << i << endl;
			}
	};
class Base3
	{
	public:
		int j;
		Base3( int j ) : j( j )
			{
			cout << "j = " << j << endl;
			}
	};

class Derived4 : public Base2, Base3
	{
	public:
		Derived4( int j, int i ) :
			Base3( j ), Base2( i )
			{
			cout << "Derived4" << endl;
			}
	};
int main( )
	{
	Derived4 d4( 18, 22 );
	}
```

``` sh
% order.exe
i = 22
j = 18
Derived4
```

The order in which constructors are run is the order in which they appear in memory

### Private members

Non-members *cannot* access `private` members

Non-members *can* access `public` members

#### Example

``` cpp
class Triangle
	{
	private:
		double a, b, c;
	public:
		double Area( ) ...
		Triangle( ) ...
		Triangle( double a, double b, double c ) ...
	};
int main( )
	{
	Triangle t( 3, 4, 5 );
	t.c = 9; //compiler error
	cout << "area = " << t.area( ) << endl;
	}
```

* By default, every member of a class is private
* A private member is visible only to other members of this class.
* The public keyword is used to signify that everything after it is visible to anyone who sees the class declaration, not just members of this class.
* Usually, we make member variables private
* public member variables often indicate a bad design

### `get` and `set` functions

#### `get`

*`public` function that returns a copy of a `private` member variable*

``` cpp
class Triangle {
	//...
	public:
		//EFFECTS: returns edge a, b, c;
		double GetA( ) { return a; }
		double GetB( ) { return b; }
		double GetC( ) { return c; }
};
```

#### `set`

*`public` function htat modifies a `private` member variable*

``` cpp
class Triangle
	{
	//...
	public:
		//REQUIRES: A, B, C are non-
		// negative and form a triangle.
		//MODIFIES: A, B, C
		//EFFECTS: sets lengths of edges.
		void SetA( double A ) { a = A; }
		void SetB( double B ) { b = B; }
		void SetC( double C ) { c = C; }
	};
```

`set` functions allow you to run extra code when a member variables changes, for example:

``` cpp
class Triangle
	{
	//...
	public:
		void setA( double A )
			{
			a = A;
			// add a check to make sure
			// a, b and c still form a
			//triangle.
			:
			}
	};
```

## Derived classes

* Also known as an ***inherited class*** or ***subclass***

### Example

This creates a new type called Isosceles that contains ***all of the Triangle member functions and member data***

Think of this as an ***"is a"*** relationship: an Isosceles ***is a*** Triangle

Notice the ***single colon*** to indicate the relationship

``` cpp
class Isosceles : public Triangle
	{
	// OVERVIEW: a representation of an
	// isosceles triangle with edge a
	// representing the base, and b = c
	// the legs
	//...
	};
```

In the above example, Triangle is the ***base class***, also known as the parent class or superclass

Isosceles is the ***derived class***, also known as the child class, inherited class, or sublclass

Isosceles ***derives from***, or ***inherits from*** Triangle

* Because member functions are inheited, we do not need to rewrite them

Below, we get memory for the member variables inherited from Triangle, plus two added member variables

``` cpp
class Isosceles : public Triangle
	{
	private:
		// New member variables.
		double Base, Leg;
	};
```

### Accessors

#### Example

Using `set` functions inherited from Triangle

``` cpp
class Isosceles : public Triangle
	{
	public:
		//EFFECTS: sets base edge a.
		void SetBase( double base )
			{
			SetA( base );
			}
		//EFFECTS: sets leg edges
		//b and c.
		void SetLeg( double leg )
			{
			SetB( leg ); SetC( leg );
			}
	};
```

### Constructors

Constructors are ***not*** inherited

``` cpp
class Isosceles : public Triangle
	{
	:
	public:
		// REQUIRES: base and leg are non-
		// negative and form an isosceles
		// triangle.
		// EFFECTS: creates an Isosceles
		// triangle with given edge lengths.
		Isosceles( double base, double leg )
			: Triangle( base, leg, leg )
			{
			}
	:
	};
```

The constructor, arguments, and order of the above code is as follows:

1. `Triangle::Triangle( 0.9, 8, 8 )`
2. `Isosceles::Isosceles( 0.9, 8 )`

#### Pitfall

Calling the base class constructor inside the derived class constructor, but outisde the intializer list creates a new, anonymous Triangle object, which is a ***local variable without a name*** inside the Isosceles constructor

``` cpp
class Isosceles : public Triangle
	{
	:
	public:
		// REQUIRES: base and leg are non-
		// negative and form an isosceles
		// triangle.
		// EFFECTS: creates an Isosceles
		// triangle with given edge lengths.
		Isosceles( double base, double leg )
			{
			Triangle( base, leg, leg ); //bad
			}
	:
	};
```

### Representation invariant

### Example

#### Triangle Invariant

Long edge is less than the sum of both short edges

#### Isosceles invariant

Base is less than sum of two legs

Legs are equal

### Overriding member functions

*a derived class has a function with the same name and prototype as the parent*

``` cpp
// An override
Triangle::SetB( double B );
Isosceles::SetB( double B );
```

Note: This is different from a function ***overload***, where a single class has two different functions with the same name, but different prototypes

### Public

*Accessible by members of the class, members of any derived classes and users of the class*

### Private

*Accessible only by members of the class*

### Protected members

*Accessible by members of the class and by members of any derived classes*

#### Example

``` cpp
class Triangle
	{
	protected:
		double a, b, c;
	};
class Isosceles : public Triangle
	{
	void SetB( double B )
		{
		b = c = B; // Allowed
		}
	void SetC( double C )
		{
		b = c = C; // Allowed
		}
	};
```

### Benefits

#### Implementation inheritence

Save implementation effort by sharing functions provided by the base class

#### Interface inheritance

Allow different derived classes to be used interchangeably through the interface provided by a common base class

### Equilateral triangle example

``` cpp
class Equilateral : public Isosceles
	{
	Equilateral( )
		{
		}
	Equilateral( double edge )
		: Isosceles( edge, edge )
		{
		}
	void Set( double A )
		{
		Triangle::Set( A, A, A );
		}
	};
```

* If we were to add a right triangle, it would be a subclass of Triangle, not Isosceles or Equilateral