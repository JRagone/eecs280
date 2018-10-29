Class 15
========

## Review

### Fill in the blank

* An ***object*** is a piece of data in memory.
* An object lives at an ***address*** in memory.
* Object lifetimes are managed according to **storage duration**.
Three options in C++:
	* ***Global (AKA Static)***
		* Lives for the whole program.
	* ***Local (AKA Automatic)***
		* Lives during the execution of its local block.
	* ***Dynamic***
		 * You control the lifetime!

### Abstraction in computer programs

* Procedural abstraction lets us separate ***what*** a procedure does from ***how*** it is implemented
* In C++, we use ***functions*** to implement procedural abstraction

`.h` files contain a description of an abstraction

### Arrays and pointers

#### Write code

``` cpp
//REQUIRES: "a" points to an array of length "size"
//EFFECTS: Returns a pointer to the first
// occurrence of "search" in "a".
// Returns NULL if not found.

int *find( int *a, unsigned int size, int search )
	{
	for ( int *i=a; i<a+size; ++i )
		if ( *i == search )
			return i; //found
	return NULL; //not found
	}
```

``` cpp
//REQUIRES: "s" is a NULL-terminated C-string
//EFFECTS: Returns a pointer to the first
// occurrence of "search" in "s".
// Returns NULL if not found.

char *strchr( char *s, char search )
	{
	while ( *s )
		{
		if ( *s == search )
			return s; //found
		++s;
		}
	return NULL; //not found
	}
```

### C strings vs. C++ strings

#### C++ string

``` cpp
#include <string>
const string hello = "hello";
hello.length( );
string s;

s = hello; //copy
if ( a == b )
	// do something
```

#### C string

``` cpp
#include <cstring>
const char *hello = "hello";
strlen( hello );
const int MAXSIZE = 1024;
char s[ MAXSIZE ];
strcpy( s, hello );
if ( strcmp( a, b ) == 0 )
	// do something
```

### C-style ADTs

Representation invariants must be observed

``` cpp
class Triangle
	{
	private:
		double a, b, c;
	public:
		double area( )
			{
			double s = ( a + b + c ) / 2;
			return sqrt( s*( s - a )*( s – b )*( s – c ) );
			}
		Triangle( )
			{ a = 0; b = 0; c = 0; }
		Triangle( double a_in, double b_in, double c_in )
			{ a=a_in; b=b_in; c=c_in; }
	};

int main( )
	{
	Triangle t(3,4,5);
	t.c = 9; //compiler error
	cout << "area = " << t.area() << endl;
	}
```

Non-members *can* access `public` members

Non-members *cannot* access `private` members

### Dynamic type

When type is not known until run time, it is called the *dynamic type*

### Derived classes and polymorphism

``` cpp
int main( )
	{
	Isosceles i;
	Triangle *t_ptr = &i;
	}

// Shape default ctor
// Triangle default ctor
// Isosceles default ctor

int main( )
	{
	Isosceles i( 1, 12 );
	Shape *s_ptr = &i;
	}

// Shape default ctor
// Triangle default ctor
// Isosceles default ctor

int main( )
	{
	Rectangle r;
	Triangle *t_ptr = &r; //compile error
	//Rectangle is not derived from Triangle
	}

int main( )
	{
	Isosceles i;
	Equilateral *e_ptr = &i; //compile error
	//Isosceles is not derived from Equilateral
	}
```

``` cpp
class Shape {
public:
	void print() const {
		cout << "Shape\n";
	}
};

int main() {
	Rectangle r(2,4);
	Isosceles i(1,12);
	Equilateral e(5);
	vector<Shape*> shapes;
	shapes.push_back(&r);
	shapes.push_back(&i);
	shapes.push_back(&e);
	for (auto shape_ptr:shapes) {
		shape_ptr->print();
	}
}

Shape default ctor
Rectangle double ctor
Shape default ctor
Triangle double ctor
Isosceles double ctor
Shape default ctor
Triangle double ctor
Isosceles double ctor
Equilateral double ctor
Shape
Shape
Shape

class Shape {
public:
	virtual void print() const;
};

int main( )
	{
	Rectangle r(2,4);
	Isosceles i(1,12);
	Equilateral e(5);
	vector<Shape*> shapes;
	shapes.push_back(&r);
	shapes.push_back(&i);
	shapes.push_back(&e);
	for (auto shape_ptr:shapes)
		shape_ptr->print();
	}

Shape default ctor
Rectangle double ctor
Shape default ctor
Triangle double ctor
Isosceles double ctor
Shape default ctor
Triangle double ctor
Isosceles double ctor
Equilateral double ctor
Rectangle
Isosceles
Equilateral
```