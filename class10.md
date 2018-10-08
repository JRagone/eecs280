Class 10
========

## Order of execution

Base class constructors run even without a constructor for the derived class

Constructors run in the order the members appear in the class

### Base class constructors always run

``` cpp
#include <iostream>
using namespace std;

class Base
	{
	public:
		int j;
		
	Base( ) : j( 3 )
		{
		}
	};
	
class Derived : public Base
	{
	public:
		int k;
	};

int main( )
	{
	Derived d;
	cout << d.j << endl; // j = 3
	cout << d.k << endl; // k = junk
	}
```

``` sh
% derived.exe
3
-858993460
```

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

## Derived types

A derived type is used to represent an ***is a*** relationship

A derived type inherits the member functions and member variables of its parent

### Upcast

Permits an object of a subclass type to be treated as an object of any supercalss type

### Downcast

When a variable of the base calss has a value of the derived class, downcasting is possible, which casts a reference of a base class to one of its derived classes

### Benefits of derived classes

#### Interface inheritence

*Save implementation effort by sharing functions provided by the base class*

We can replace any `Triangle` object in a program with an `Isosceles` object and the program will still work

##### Liskov Substitution Principle

***If S is a subtype of T, then objects of
type T may be replaced with objects
of type S***

***Functions that use pointers to base
classes can use objects of derived
classes without knowing it***

This is upcasting

#### Interface inheritence

*Allow different derived classes to be used interchangeably thorugh the interface provided by a common base class*

### Static type

Often, the compiler can tell which derived type is needed

This is called the *static type*

``` cpp
Triangle t( 3, 4, 5 );
t.print( );

Triangle *t_ptr = &t;
t_ptr->print( );

Isosceles i( 1, 12 );
i.print( );

Isosceles *i_ptr = &i;
i_ptr->print( );
```

``` sh
./a.out
Triangle: a=3, b=4, c=5
Triangle: a=3, b=4, c=5
Isosceles: base=1, leg=12
Isosceles: base=1, leg=12
```

### Dynamic type

Other times, the type is not known until run time

This is called the *dynamic type*

#### Example

``` cpp
Triangle * ask_user( );

int main( )
	{
	Triangle *t = ask_user( ); //enters "Isosceles"
	t->print( );
	}
```

The static type of `t` is `Triangle *`

The dynamic type of `t` is `Isosceles *`

#### `new`

To create an object that won't go out of scope when the function returns, we use `new`

``` cpp
Triangle *ask_user( )
	{
	cout << "Triangle, Isosceles "
		"or Equilateral?" << endl;
	string s;
	cin >> s;
	
	if ( s == "Triangle" )
		return new Triangle( 3,4,5 );
	if ( s == "Isosceles" )
		return new Isosceles( 1,12 );
	if ( s == "Equilateral" )
		return new Equilateral( 5 );
	
	cout << "Unrecognized shape '"
		<< s << "'\n";
	exit( 1 ); //crash
	}
```

Always safe to upcast, so returning `Isosceles` or `Equilateral` is safe

#### `delete`

Always goes together with `new`

``` cpp
int main( )
	{
	Triangle *t = ask_user( );
	//enters "Isosceles"
	t->print( );
	delete t;
	}
```

``` sh
./a.out
Triangle, Isosceles or Equilateral?
Isosceles
Triangle: a=1 b=12 c=12
```

* `t` can change types at runtime, in other words it is ***polymorphic***
* We can use the ***virtual function*** mechanism in C++ to check the *dynamic type* of `t` at runtime and call the correction version of `print()`

### Polymorphism

*The ability to associate many behaviors with one function name*

A polymorphic type is any type with a virtual function

Virtual functions are the C++ mechanism used to implement polymorphism

#### Example

`virtual` means "check the dynamic type at runtime, then select the correct `print( )` member funciton

`virtual` is inherited so the overridden `print( )` in `Isosceles` and `Equilateral` will automatically become `virtual`

``` cpp
class Triangle
	{
	virtual void print( ) const
		{
		:
		}
	:
	};
```

Now the orignal program works correctly

``` sh
./a.out
Triangle, Isosceles or Equilateral? Isosceles
Isosceles: base=1 leg=12
```

#### Exercise

`Rectangle` *is a* `Shape`

``` cpp
class Shape
	{
	public:
		virtual double area( ) const {/*...*/}
		virtual void print( ) const {/*...*/}
	};
class Triangle : public Shape {/*...*/};
class Rectangle : public Shape {/*...*/};
```

Although our code is perfectly legal C++, `Shape` has no member variables, and something like

``` cpp
Shape s;
s.print( );
```

makes no sense and will not compile!

A shape is an abstract idea, and our shape should only be an interface to ensure that all shapes behave the same way

### Abstract class

You cannot create an instance of an abstract class, which is what we want for a `Shape`

An abstract class will force derived types to all behave the same way

### Pure virtual functions

*Each method is declared a `virtual` function and "Assigns" a 0 to each of these `virtual` functions*

#### Example

``` cpp
class Shape
	{
	public:
		virtual double area( ) const = 0;
		void print( ) const = 0;
	};
```

* You *can* create a pointer to an abstract class, and then assign the pointer to a *concrete class* derived from the base class

``` cpp
Rectangle r(2,4);	//concrete derived type
Shape *s = &r;		//OK, Rectangle is a Shape
s->print();			//virtual, so correct version
					//of print() is called
```

``` sh
./a.out
Rectangle: a=2 b=4
```

### Factory functions

*Creates objects for another programmer who doesn't need to know their actual types*

``` cpp
//EFFECTS: asks user to select a shape
//		   returns a pointer to correct object
Shape * ask_user( );
```

`ask_user( )` is an example of a *factory function*

### Upcast

Conversion from `Isosceles` to `Shape` is called an *upcast*

In `ask_user( )`, the type conversion is automatic, an *implicit cast*

### Downcast

*A cast from one type in the class hierarchy to a lower one*

Since `Shape` might not be `Isosceles`, this cas cannot happen automatically

#### `dynamic_cast`

`dynamic_cast<T*>(ptr)` downcasts `ptr` to type `T*`, if possible. Otherwise, it returns 0.

This is usually a symptom of a poor design