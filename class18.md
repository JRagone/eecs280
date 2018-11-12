Class 18
========

## Iterators

*An iterator allows you to traverse a container*

``` cpp
int a[ SIZE ]; // fill a
for ( int i = 0; i < SIZE; ++i )
	cout << a[ i ] << endl;
```

We will use procedural abstraction and data abstraction to iterate over a container

### Templated linked list

``` cpp
int main( )
	{
	List<int> il; // ( )
	il.PushFront( 1 ); // ( 1 )
	il.PushFront( 2 ); // ( 2 1 )
	cout << il.Front( ); // 2
	il.PopFront( ); // ( 1 )
	il.PopFront( ); // ( )
	cout << il.IsEmpty( ); // true
	}
```

Our iterator will work a lot like using a pointer to traverse an array

``` cpp
int a[ SIZE ];
// Fill a with data, then print.
for ( int *p = a; p < a + SIZE; ++p )
	cout << *p << endl;

List< int > b;
// Fill b with data, then print.
for ( List<int>::Iterator i = b.begin( ); i != b.end(); ++i )
	cout << *i << endl;
```

### Iterator functions

``` cpp
List< int > b;
for ( /*1*/ List<int>::Iterator i = b.begin( ); /*4*/i != b.end( ); /*3*/++i )
	cout << /*2*/*i << endl;
```

1. Create an iterator with a constructor
2. Get the `T` at the iterator's current position
3. Move the iterator to the next position
4. Compare two iterators

### Implementation

We'll have two classes:

1. One `List` class, that holds `T`'s
2. One `Iterator` class, that returns each `T` in the `List`

#### Problems

There are two problems we need to solve:

1. For the `Iterator` to do its job, it needs to have access to the `Node` type, which is a private type inside `List`
2. The `Iterator` needs to return a type `T`, which should be the *exact same type `T`* that is inside the `List`

#### Solution

We can solve both problems by declaring the `Iterator` class ***inside*** the `List` class

From the outside, we can refer to our new `Iterator` class by specifying the scope: `List<int>::Iterator`

#### `Iterator`

We can use a `Node*` pointer to keep track of the current node

``` cpp
class Iterator
	{
	private:
		Node *node_ptr;
	public:
		Iterator( );	// Create
		T& operator*( ) const;	// Get
		Iterator& operator++( );	// Move
		bool operator!=( Iterator rhs )
			const;	//Compare
	};
```

The invariant on `node_ptr` is that is points to the current node in the underlying `List`, and `nullptr` (same as `NULL` or `0`) otherwise

***List does not need to change***

``` cpp
Iterator( ); // Create new Iterator
T &operator*( ) const; // Get T from node
Iterator &operator++( ); // Move to next node
bool operator!=( Iterator rhs ) const; // Compare two Iterators

int main( )
	{
	List<int> b; // fill b
	for ( List<int>::Iterator i = b.begin( ); i != b.end( ); ++i )
		cout << *i << endl;
	}
```

### Create new iterator: `Iterator()`

Now we can define (implement) each `Iterator` method

#### Constructor

The constructor establishes the invariant on `node_ptr`

It's okay to implement short functions in the class declaration

``` cpp
class List
	{
	public:
		class Iterator
			{
				Node *node_ptr;
			public:
				Iterator( ) : node_ptr( nullptr )
					{
					}
				// "past the end" by
				// default
			// ...
			};
	};
```

#### Get `T` at the iterator's current position

The dereference operator returns a reference to the `datum` at the current `Iterator` position

``` cpp
class List
	{
	public:
		class Iterator
			{
				Node *node_ptr;
			public:
				T &operator*( ) const
					{
					assert( node_ptr != nullptr );
					return node_ptr->datum;
					}
				// ...
			};
	};
```

The `assert()` stops execution is you try to use an iterator "pass the end"

The `Iterator` has a pointer to the `Node` created by the `List`, and the `Node`'s data members are all public because it is a `struct`

#### Move to next position: `operator++`

The prefix increment operator moves to the next `List` node

``` cpp
class List
	{
	public:
		class Iterator
			{
				Node *node_ptr;
			public:
				Iterator &operator++( )
					{
					assert( node_ptr );
					node_ptr = node_ptr->next;
					return *this;
					}
				// ...
			};
	};
```

#### Comparing iterators: `!=`

We need a way to compare two iterators

``` cpp
class List
	{
		// ...
		class Iterator
		{
			Node *node_ptr;
		public:
			bool operator!=
				( Iterator rhs ) const
				{
				return node_ptr !=
				rhs.node_ptr;
				}
			// ...
		};
	};
```


#### Missing piece

We need to know where to start and where to end, and this ability is typically implemented as member functions called `begin()` and `end()` inside the container, not in the iterator

#### Implementing `end()`

Returns a default `Iterator` object, "past the end" position

``` cpp
class List
	{
	public:
		class Iterator { /*...*/ };
		Iterator end( ) const
			{
			return Iterator( );
			}
	};
```

#### Implementing `begin()`

Returns an `Iterator` object pointing to the first `List` position

``` cpp
class List
	{
		Node *first; // points to
				// first Node
		// ...
		class Iterator { /*...*/ };
		Iterator begin( ) const
			{
			return Iterator( first );
			}
	};
```

We need another `Iterator` constructor with a `Node*` input

No one outside `List` should see this, so make it `private`

``` cpp
class List
	{
	public:
		class Iterator
			{
				// ...
			private:
				Iterator( Node* p ) : node_ptr( p )
					{
					}
			};
		Iterator begin( ) const
			{
			return Iterator( first );
			}
	};
```

##### Problem

* `Iterator( Node* p )` is private to the `Iterator` class
* `begin( )` is a member function of `List`, not `Iterator`
* Therefore, `begin( )` cannot access `Iterator( Node* p )`

##### Solution: `friend` classes

The `friend` declaration allows you to expose the private members of one class to another class (and only that class)

``` cpp
class List
	{
	public:
		class Iterator
			{
			// ...
			private:
			Iterator( Node* p ) :
				node_ptr( p ) { }
			friend class List;
			};
		Iterator begin( ) const
			{
			return Iterator( first );
			}
	};
```

Now, `begin()` can access `Iterator(Node* p)`, but clients of `List` cannot

Understanding that friendship is something given, not taken, will help you remember that "`friend class List;`" goes inside `Iterator`, not the other way around

### Example

``` cpp
List<int> il;
il.PushFront( 3 );
il.PushFront( 2 );
il.PushFront( 1 );

for ( List<int>::Iterator i = il.begin( ); i != il.end( ); ++i )
	cout << *i << " ";

cout << endl;
```

``` sh
./a.out
1 2 3 
```

### Example with C++11

*Range for* automatically starts at `il.begin()` and ends before `il.end()`

*Range for* works with any container that has a "standard" iterator like our `List`

``` cpp
List<int> il;
// fill il

for ( auto i:il )
	// i is as copy of a datum
	cout << i << " ";

cout << endl;
```

``` sh
./a.out
1 2 3 
```

### Using iterators

This allows us to write any number of algorithms that must examine every entry of a list, without:

* Needing to udnerstand the mechanics of how `List`s actually work
* Requiring that the designer of the `List` class knew everything that the client wanted in advance

In other words, we have successfully created a function abstraction to access a container

### Mutliple iterators

Sine the `Iterator` is an object, we can have more than one pointing to the same `List`

``` cpp
List<int> il; //fill with ( 2 3 )
auto i = il.begin( ); // List<int>::Iterator
auto j = i; // List<int>::Iterator
++j;
cout << "*i = " << *i << endl;
cout << "*j = " << *j << endl;
```

``` sh
./a.out
*i = 2
*j = 3
```

#### Example

A function that checks a list for duplicates

Below is code that works for an array

``` cpp
// EFFECTS: returns true if a
// contains no duplicates

bool HasNoDuplicates( int a[ ], int size )
	{
	for ( int i = 0; i < size; ++i )
		for ( int j = i + 1; j < size; ++j )
			if ( a[ i ] == a[ j ] )
				return false;
	return true;
	}
```

Below is code that works for `List`

``` cpp
bool HasNoDuplicates( const List<int> &l )
	{
	for ( auto i=il.begin( ); i != il.end( ); ++i )
		{
		auto j = i;
		++j; //this will increment an iterator
		for ( ; j != il.end( ); ++j )
			if ( *i == *j )
				return false;
		}
	return true;
	}
```

We ***cannot*** use *Range for* with `i`, because `i` would be an `int`, not an `Iterator`

### Big Three

Do we need it? `Iterator` has one member variable `Node *node_ptr`, which is a pointer to dynamically allocated memory

**No**, because memory is already managed by the `List`:

1. If you allocated the memory with `new`, you need to deallocate it with `delete`
2. `Iterator` does not allocate memory, and therefore does not need to deallocate
3. `List` allocates memory for its nodes, and also deallocates

### Iterator invalidation

Once an iterator is created, if the underlying container is modified, the iterator *may* become invalid, dependent on the container

If the iterator is invalidated, its behavior is undefined, much like an invalid pointer

The intuition behind this is that the iterator depends on the representation of the container - if that changes, the iterator is likely to miss an element or return an element that no longer exists

It is *your* responsibility to keep your `Iterator` within the bounds of your container

``` cpp
#include "List.h"
int main( )
	{
	List<int> il; //fill with ( 2 3 )
	auto i = il.begin( );
	il.PopFront( );
	cout << *i; //Undefined!
	}
```

### Iterators and pointers

* Iterators have a lot in common with pointers
* Both can refer to objects that don't exist any more
* Be careful!