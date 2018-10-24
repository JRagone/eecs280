Class 14
========

## Class with dynamic arrays

``` cpp
class UnorderedSet
	{
		int *elements; //pointer to dynamic array
		int numberOfElements; //current occupancy
		int NumberAllocated; //capacity of array
		static const int DefaultNumberAllocated = 100;
	public:
		//...
	};
```

`elements` will point to a dynamic array
`NumberAllocated` tells us the size of the allocated array
`DefaultNumberAllocated` tells us the initial size

## UnorderedSet constructor

The constructor will now allocate a default size array

``` cpp
UnorderedSet::UnorderedSet( )
	: numberOfElements ( 0 ),
	NumberAllocated( DefaultNumberAllocated )
	{
	elements = new int[ NumberAllocated ];
	}
```

## Alternate constructor

We can write an alternate constructor that has the same name as the default, but a different type signature

This is called ***function overloading***: two different functions with the same name, but different prototypes

## Default arguments

Using `parameter = value` in the parameter prototype of a function sets the default value of the parameter to `value`

## Dynamic memory problems

``` cpp
void foo( )
	{
	UnorderedSet s( 3 );
	} //foo returns

int main( )
	{
	foo( );
	}
```

Array is still on the heap, so there is a memory leak

## Destructors

To solve the above memory leak, we use a ***destructor*** to call `delete` when an `UnorderedSet` is destroyed

``` cpp
class UnorderedSet
	{
	public:
		//...
		//EFFECTS: destroys this UnorderedSet
		~UnorderedSet( );
		
		//...
	};

UnorderedSet::~UnorderedSet( )
	{
	delete[ ] elements;
	}
```

Destructors run automatically when an object is destroyed

If it is a local variable, this is when it goes out of scope

If it is a global variable, this is when the program ends

``` cpp
int foo( )
	{
	UnorderedSet p; //p ctor runs
	} //p dtor runs

UnorderedSet s;

//s ctor runs
int main( )
	{
	} //s dtor runs
```

## Memory leaks

``` cpp
int main( )
	{
	new UnorderedSet( 3 );
	}

// Destructor does not run
3*sizeof(int) + //int[3]
sizeof(int*) + //elements
sizeof(int) + //NumberAllocated
sizeof(int) //numberOfElements
= 28 bytes leaked
```

This can be fixed with the below:

``` cpp
int main( )
	{
	//remember location
	//of UnorderedSet on heap
	UnorderedSet *ptr =
		new UnorderedSet( 3 );	//ctor runs
	
	//call delete, which
	//causes dtor to run
	delete ptr;
	ptr = nullptr;	//dtor runs
	}
```

## RAII: Resource Acquisition Is Initialization

Observation: the compiler is terrible at managing resources

* If we use new to create dynamic memory, the compiler can’t clean it up for us

Observation: the compiler is good at managing local variables

* Automatic storage duration
* Cleaned up when they go out of scope

Solution: wrap up resource management in a class and use local instances of the class to access the resources

## Modifying `Insert( )`

### Old version

``` cpp
void UnorderedSet::Insert( int v )
	{
	assert( numberOfElements < NumberAllocated ); //full!
	if ( index_of( v ) != NumberAllocated )
		return;
	elements[ numberOfElements++ ] = v;
	}
```

### New version

``` cpp
void UnorderedSet::Insert( int v )
	{
	if ( index_of( v ) != NumberAllocated )
		return;
	if( numberOfElements == NumberAllocated )
		grow( );
	elements[ numberOfElements++ ] = v;
	}
```

### `grow( )`

Grow must perform the following steps:

1. Allocate a bigger array
2. Copy the smaller array to the bigger one
3. Destroy the smaller array
4. Modify `elements` and `NumberAllocated` to reflect the new array

``` cpp
void UnorderedSet::grow( )
	{
	int *tmp = new int[ NumberAllocated + 1 ]; // 1
	for ( int i = 0; i < numberOfElements; ++i )
		tmp[ i ] = elements[ i ] ; // 2
	delete[ ] elements; // 3
	elements = tmp;
	NumberAllocated += 1; // 4
	}
```

#### Upper bound

`grow( )` is called when inserting a new value makes `numberOfElements` greater than `NumberAllocated`

All the elements of an array are copied

As the size of the `UnorderedSet` grows O(n), the cost to build the `UnorderedSet` grows much faster at O(n<sup>2</sup>)

#### Optimization

Right now, we copy `n` things, but only allocate room for one more thing

We will change `grow( )` to allocate room for `n` more things; we will double the array instead of increasing it by one

``` cpp
void UnorderedSet::grow( )
	{
	int *tmp = new int[ NumberAllocated * 2 ];
	for ( int i = 0; i < numberOfElements; ++i )
		tmp[ i ] = elements[ i ];
	
	delete[ ] elements;
	elements = tmp;
	NumberAllocated *= 2;
	}
```

This implementation decreases the performance cost to O(2N)

## Deconstructors and polymorphism

``` cpp
class Animal
	{
	virtual void talk( ) {}
	};

class Chicken : public Animal
	{
	public:
		virtual void talk( )
			{
			cout << "cluck\n";
			}
	};
	
class Horse : public Animal
	{
	public:
		virtual void talk( )
			{
			cout << “neigh\n";
			}
	};
```

``` cpp
int main( )
	{
	//input: "Chicken"
	Animal *a = ask_user( );
	//do something with a
	delete a;
	a = nullptr;
	}
```

This calls the `~Animal( )` destructor, because static type of `a`, `Animal`, is different from dynamic type of `Chicken`

To fix this, polymorphic objects need virtual destructors

Put another way: if you have a virtual function and a destructor, then the destructor probably needs to be virtual too.

``` cpp
class Animal
{
	public:
		virtual void talk( )
			{
			}
		virtual ~Animal( )
			{
			}
};
```

Since the destructor is virtual, the correct destructors are selected at runtime
