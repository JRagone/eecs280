Class 16
========

## Classes with dynamic arrays

Before

``` cpp
class UnorderedSet
	{
	public:
		static const int NumberAllocated = 100;
	private:
		int elements[ NumberAllocated ];
		int numberOfElements ;
		//...
	};
```

After

``` cpp
class UnorderedSet
	{
	private:
		int *elements; //pointer to dynamic array
		int numberOfElements; //current occupancy
		int NumberAllocated; //capacity of array
		static const int DefaultNumberAllocated = 100;
		//...
	};
```

## Function overloading vs overriding

An alternate constructor is an instance of ***function overloading***: two different functions with the same name, but different prototypes

``` cpp
UnorderedSet s1;	//No arguments
	//default ctor runs

UnorderedSet s2( 200 );	//Integer argument
	//alternate ctor runs
```

This is different form ***function overriding***, where a derived class has a function with the same name and prototype as the parent

## Default arguments

Reduce duplication of code for almost identical constructors by using ***default arguments***. With these, we can define ***only one*** constructor, but make its argument optional

``` cpp
class UnorderedSet
	{
	public:
		//EFFECTS: creates an UnorderedSet with specified capacity
		//REQUIRES: capacity > 0
		UnorderedSet( int capacity = DefaultNumberAllocated );
		//...
	};
	
UnorderedSet::UnorderedSet( int capacity )
	: numberOfElements ( 0 ), NumberAllocated( capacity )
	{
	elements = new int[ NumberAllocated ];
	}
```

## Destructor

A destructor runs automatically:

* Local variable: when it goes out of scope
* Global variable: when the program ends
* Dynamic variable: when it is deleted

``` cpp
class UnorderedSet
	{
	public:
		~UnorderedSet( )
			{
			delete [ ] elements;
			}
		//...
	};

void foo( )
	{
	UnorderedSet s( 3 );
	} //~UnorderedSet() runs

int main( )
	{
	foo( );
	}
```

## Copy constructor

A *copy constructor* lets us customize how a class is copied

### `UnorderedSet`

``` cpp
class UnorderedSet
	{
	public:
		//EFFECTS: create an UnorderedSet
		//that is a copy of another.
		UnorderedSet( const UnorderedSet &other );
		//...
	};
```

* Like other constructors, the copy constructor must create a new `UnorderedSet`, starting from a formless blob of memory
* Unlike other constructors, the copy constructor must copy everything from the other `UnorderedSet`

* The default constructor had one task

	1. Initialize the member variables

* The copy constructor has two tasks

	1. Initialize the member variables
	2. Copy everything from the other `UnorderedSet`

``` cpp
UnorderedSet::UnorderedSet( const UnorderedSet &other )
	{
	//1. Initialize member variables
	elements = new int[ other.NumberAllocated ];
	numberOfElements = other.numberOfElements;
	NumberAllocated = other.NumberAllocated;
	
	//2. Copy everything from the other UnorderedSet
	for ( int i = 0; i < other.numberOfElements; ++i )
		elements[ i ] = other.elements[ i ];
	}
```

**An `UnorderedSet` method has access to the `private` members of both `this` instance and the `other` instance**

### Deep copies

The old copy constructor (provided by the compiler) copies pointers. This is called a ***shallow copy***

The new copy constructor copies the things hte pointers point to. This is called a ***deep copy***

## Problems with assignment `=`

``` cpp
int main( )
	{
	UnorderedSet s1( 3 );
	UnorderedSet s2( 6 );
	s2 = s1;
	}
```

This leaks memory when `s2 = s1`, because the `=` operator uses the old copy constructor, a **shallow copy**, and this makes accessing and deleting the memory allocated on the heap by `s2` unaccessible. `int[6]` on the heap leaks memory

We want a **deep copy** from `s1` to `s2`. To do this we redefine the *assignment operator* for an `UnorderedSet` by using ***operator overloading***

### Operator overloading

*Operator overloading* lets us customize what happens when we use a built-in symbol

## Implementing `=`

Our overloaded assignment operator will share some code in common with the destructor and the copy constructor

1. Destructor `~UnorderedSet( )`
	1. Delete dynamic array
2. Copy constructor `UnorderedSet( const UnorderedSet &other )`
	1. Initialize member variables
	2. Copy variables and dynamic array from other UnorderedSet
3. Overloaded assignment operator
`UnorderedSet &operator=( const UnorderedSet &rhs )`
	1. Delete dynamic array
	2. Copy variables and dynamic array from rhs UnorderedSet

``` cpp
UnorderedSet &UnorderedSet::operator=( const UnorderedSet &rhs )
	{
	//delete dynamic array
	delete[ ] elements;
	
	// Copy member variables from rhs.
	elements = new int[ rhs.NumberAllocated ];
	numberOfElements = rhs.numberOfElements
	NumberAllocated = rhs.NumberAllocated;
	
	// Copy the array.
	for (int i = 0; i < rhs.numberOfElements; ++i)
		elements[i] = rhs.elements[i];
	return *this;
	}
```


### Understanding `this`

Every member function has "secret" input called `this`

``` cpp
class UnorderedSet
	{
	public:
		UnorderedSet( UnorderedSet *this);
		void insert( UnorderedSet *this, int v );
		void remove( UnorderedSet *this, int v );
		bool query( UnorderedSet *this, int v ) const;
		int size( UnorderedSet *this ) const;
		void print(UnorderedSet *this) const;
		//...
	};
```

***Think of `this` as a local variable which points to the current instance***

### bug in `=`

``` cpp
int main( )
	{
	UnorderedSet s( 3 );
	s.insert( 5 );
	s = s;
	}
```

The unordered set will delete its own array in this implementation!

### Corrected implementation

``` cpp
UnorderedSet & UnorderedSet::operator=( const UnorderedSet &rhs )
	{
	if ( this == &rhs )
		return *this; //fix "s = s"
	delete[ ] elements; //remove all
	
	elements = new int[ rhs.NumberAllocated ];
	numberOfElements = rhs.numberOfElements;
	NumberAllocated = rhs.NumberAllocated;
	
	for ( int i = 0; i < rhs.numberOfElements; ++i )
		elements[ i ] = rhs.elements[ i ];
	
	return *this;
	}
```

## The Rule of the Big Three

If you have any dynamically allocated storage in a class, you must provide:

1. A destructor
2. A copy constructor
3. An overloaded assignment operator
