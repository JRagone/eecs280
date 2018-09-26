Class 6
=======

## Kinds of objects in C++

### Primitives

* `int`, `double`, `char`, etc.
* Pointers

### Arrays (homogenous)

* A *contiguous* sequence of objects of the same type

### Compound (heterogeneous)

* A ***compound type*** is a datatype that we define in our code as containing other primitive or compound data types
* A compound object made up of *member* subobjects
* The members and their types are defined by a ***struct*** or a ***class***

## Struct

1. The simplest compound type in C and C++ is the ***struct***
2. Elements in a struct are laid out in memory in the order listed

### Example

``` cpp
struct Triangle
	{
	double a, b, c; // Edges
	};
```

* This struct contains a triangle's edge lengths
* It declares a ***new type `Triangle`*** but does not define any variables of that type
* A common convention is to capitalize the names of structs

### Another example

``` cpp
struct Triangle
	{
	double a, b, c; // Edges
	char name[ 100 ];
	int id;
	};
```

* A struct can contain different types
* This struct contains three doubles for the edges, a 100-character C-string and an int

### Example with main

``` cpp
struct Triangle
	{
	double a, b, c; // Edges
	};

int main( )
	{
	Triangle t;
	}
```

The values of `a`, `b`, and `c` are undefined

### Initializing a struct

Members can be intialized with dot notation or with a list

``` cpp
struct Triangle
	{
	double a, b, c; // Edges
	};

int main( )
	{
	Triangle t;
	t.a = 3;
	t.b = 4;
	t.c = 5;
	}
```

``` cpp
struct Triangle
	{
	double a, b, c; // Edges
	};

int main( )
	{
	Triangle t = { 3, 4, 5 };
	}
```

### Struct assignment

``` cpp
struct Triangle
	{
	double a, b, c; // Edges
	};

int main( )
	{
	Triangle t = { 3, 4, 5 };
	Triangle t2 = t1;
	}
```

### Passing a struct to a function

Passing structs by value is inefficient, as it means you are copying the entire struct onto the stack.

It's better to either:

#### Pass by pointer

``` cpp
struct Triangle
	{
	double a, b, c; // Edges
	};
	
double TriangleArea( Triangle *t )
	{
	double s = ( t->a + t->b + t->c ) / 2;
	double area = sqrt( s*( s - t->a )*( s - t->b )*( s - t->c ) );
	return area;
	}

int main( )
	{
	Triangle t = { 3, 4, 5 };
	cout << "area = " << TriangleArea( &t ) << endl;
	}
```

The following pointer dereference notations are identical

``` cpp
(*t).a
t->a
```

However, the following notation ***does not*** work

``` cpp
*t.a
```

This does not work, because the dot takes precedence over the dereference, so the `a` component is being taken before `t` is referenced

### Pass by reference

``` cpp
struct Triangle
	{
	double a, b, c; // Edges
	};
	
double TriangleArea( Triangle &t )
	{
	double s = ( t.a + t.b + t.c ) / 2;
	double area = sqrt( s*( s - t.a )*( s - t.b )*( s - t.c ) );
	return area;
	}

int main( )
	{
	Triangle t = { 3, 4, 5 };
	cout << "area = " << TriangleArea( t ) << endl;
	}
```

## const

* const means do not modify in this scope

``` cpp
const int MaxSize = 10;

int main( )
	{
	MaxSize = 7;			// Error: MaxSize
					// is read-only
	char str[ MaxSize ] = "hello";	// OK
	}
```

The ***value of the pointer*** and the ***value of the object to which the pointer points*** can be made constant

``` cpp
const T *p;		// "T" (the pointed-to object)
			// cannot be changed
T *const p;		// "p" (the pointer) cannot be
			// changed
const T *const p;	// neither can be changed
```

`T` is the type, such as `int`, and `p` is the pointer, such as a variable name

### Example

The following parameters are identical

``` cpp
int strlen( char const *s )
	{
	s += 1;		//OK
	*s = 'j';	//error
	//...
	}
	
int strlen( const char *s )
	{
	s += 1;		//OK
	*s = 'j';	//error
	//...
	}
```

An error is thrown, because the value of the object to which the pointer points is constant, and cannot be modified.

The value of the pointer, however, is not constant, and is open to modification.

### Another example

``` cpp
int strlen( char *const s )
	{
	s += 1;		//error
	*s = 'j';	//OK
	//...
	}
```

An error is thrown, because the value of the pointer is constant, and cannot be modified.

The value of the object to which the pointer points, however, is not constant, and is open to modification.

### Third example

``` cpp
int strlen( const char *const s )
	{
	s += 1;		//error
	*s = 'j';	//error
	//...
	}
```

Both the pointer and the object are constant in this situation, so changes to either will result in an error.

## Composable data types

### Mechanisms to extend basic data types

#### Arrays

``` cpp
int a[ 3 ] = { 1, 2, 3 };
```

#### Pointers

``` cpp
int *p = a;
```

#### References	

``` cpp
int &a_ref = a[ 0 ];
```

#### Structs	
``` cpp
struct x
	{
	int i;
	char c;
	}
```

Types are composable. Once you have declared a type struct x, that type can be used to define a pointer to struct x, an array of struct x, or even a struct that contains a struct x.

## Struct vs. array

|struct|array|
|---|---|
|`struct Triangle { /*...*/ };`|`double edges[ 3 ];`|
|Stores group of data|Stores group of data|
|Heterogeneous - Different types|Homogeneous - All the same type|
|Access by name|Access by index|
|Default pass-by-value|Default pass-by-pointer|
|Creates a custom type|Does not create a new type|
