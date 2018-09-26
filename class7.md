Class 7
=======

## Compound objects review

A ***compound type*** is a datatype that we define in our code as containing other primitive or compound data types

### Kinds of objects in C++

#### Primitives

* `int`, `double`, `char`, etc.
* Pointers

#### Arrays (homogeneous)

* A *contiguous* sequence of objects of the same type

#### Compound (heterogeneous)

* A compound object made up of ***member*** subobjects
* The members and their types are defined by a ***struct*** or a ***class***

## C-style Abstract Data Types

### Struct

* The simplest compound type in C and C++ is the ***struct***
* Elements in a struct are laid out in memory in the order listed

#### Pass by pointer

``` cpp
double TriangleArea( Triangle *t );
```

#### Pass by reference

``` cpp
double TriangleArea( Triangle &t );
```

#### pointer-to-struct

There are two right ways to access the data inside a struct passed by pointer and one wrong way

``` cpp
double TriangleArea( Triangle *t )
	{
	( *t ).a + ...	// Deref the pointer to get the object
	t->a + ...		// The most commonly-used notation
	*t.a + ...		// Does not work, because the dot takes precedence over the dereference
	}
```

### Const

Given a pointer, there are two things you can make constant:

1. The value of the pointer
2. The value of the object the pointer points to

``` cpp
const T *p;			// "T" (the pointed-to object)
						// cannot be changed
T *const p;			// "p" (the pointer) cannot be
						// changed
const T *const p;	// neither can be changed
```

### Composable data types

Types are composable.

Once you have declared a type struct x, that type can be used to define a pointer to struct x, an array of struct x, or even a struct that contains a struct x.

``` cpp
struct x
	{
	int i;
	char c;
	};

struct y
	{
	x p, *q, r[ 2 ];
	};
```

### Procedural abstraction

***Procedural abstraction*** lets us separate ***what*** a procedure does from ***how*** it is implemented

In C++, we use functions to implement procedural abstraction

``` cpp
//MODIFIES: v
//EFFECTS: sorts v
void sort( std::vector<double> v );

//sort from the standard library
void sort( std::vector<double> &v )
	{
	std::sort( v.begin( ), v.end( ) );
	}
```

It doesn't matter to the user of the interface how `sort()` is implemented, just that it works the way the signature describes

### Data abstraction

*Lets us separate* ***what*** *a type is (and what it can do) from* ***how*** *the type is implemented*

1. In C-style code, use a `struct` to implement data abstraction
2. The abstractions we create are called Abstract Data Types (ADTs)
3. ADTs let us represent more complex data and more complex relationship between data
4. We are no longer limited to `int`, `double`, and other primitives
5. ADTs make programs easier to maintain and modify by partitioning what has to be known
6. You can change the implementation adn users of the type should generally not care

#### Two programmers

Two programmers make a triangle Abstract Data Type

1. Alice and Bob agree on an abstraction `Triangle.h`
2. Alice codes `Triangle.cpp`, implementing the ADT
3. Bob codes `Graphics.cpp`, using the ADT

### Initialization

ADTs need rules for initialization. For example, a 3, 4, 9 triangle should not be legal, because those side lengths do not form a triangle

### Representation invariant

We use ***representation invariants*** to express the conditions for a ***valid*** compound object

#### Example

##### `Triangle.h`

``` cpp
void TriangleInit( Triangle*t, double a_in, double b_in, double c_in );
```

##### `Triangle.cpp`

``` cpp
//REQUIRES a_in, b_in, and c_in are greater than 0
// and form a triangle

void TriangleInit( Triangle *t, double a_in, double b_in, double c_in )
	{
	t->a = a_in;
	t->b = b_in;
	t->c = c_in;
	assert(	( t->a + t->b > t->c ) &&
				( t->a + t->c > t->b ) &&
				( t->b + t->c > t->a ) );
	}
```

### General rule of abstraction

*If another programmer uses my code, they should not have to change their code if I change my implementation but not my interface*

#### C code: Informal agreement between coders

* Struct definitions go into header (.h) file with comments explaining how they're *supposed* to be used

#### C++ code: Strictly enforced by the compiler

* Classes added, bringing language support for public/private, accessors/setters, constructors/destructors, and member functions

## Testing C-style ADTs

### Types of testing

#### Unit test

Test one piece, e.g., one function, at a time. Find and fix bugs early with smaller, less complex, easier to understand tests

#### System test

Tets the entire system after all the individually-tested pieces have been integrated

#### Regression test

Automatically run all unit and system tests after a code

* Simple
* (Edge) Special
* Stress

## Streams and stringstreams

### Input

* `istream`
* `ifstream`
* `istringstream`

To read input from a stream, use the extraction operator `>>`

#### `istringstream`

* An input stream that uses a string as its source
* Useful for simulating stream input from a "hardcoded" string

``` cpp
int main( )
	{
	// A hardcoded PPM image
	string input = "P3\n2 2\n255\n255 0 0 0 255 0 \n";
	input += "0 0 255 255 255 255 \n";
	
	// Use istringstream for simulated input
	istringstream ss_input( input );
	Image img;
	Image_init( &img, ss_input );
	
	assert( Image_width( &img ) == 2 );
	Pixel red = { 255, 0, 0 };
	assert( Pixel_equal( Image_get_pixel( &img, 0, 0 ), red ) );
	}
```

### Output

* `ostream`
* `ofstream`
* `ostringstream`

To write output into a stream, use the insertion operator `<<`

#### `ostringstream`

* An output stream that writes into a `string`
* Useful for capturing output as a `string` that can be checked for correctness

``` cpp
int main( )
	{
	Matrix mat;
	MatrixInit( &mat, 3, 3 );
	MatrixFill( &mat, 0 );
	MatrixFill_border( &mat, 1 );
	
	// Hardcoded correct output
	string output_correct = "3 3\n1 1 1\n1 0 1\n1 1 1\n";
	
	// Capture output in ostringstream
	ostringstream ss_output;
	Matrix_print( &mat, ss_output );
	assert( ss_output.str( ) == output_correct );
	}
```