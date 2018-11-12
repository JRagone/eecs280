Class 17
========

## Sequential contianers

A *sequence container* stores elements in order

One option: store elements contiguously in memory

### Contiguous memory

#### Pros

Fast indexing pointer arithmetic under the hood

#### Cons

Increasing the capacity requires allocating a new array

Slow insert in the middle

### Non-contiguous memory

We could also store locations with *pointers*

## Linked lists

### Nodes

``` cpp
struct Node
	{
	int datum;
	Node *next;
	};
```

Each node of the list includes a `datum`, but also a `next` pointer containing the address of the next node

This structure is called a *linked list*

### Representation

``` cpp
// One node in the list.
struct Node
	{
	int datum;
	Node *next;
	};

// The entire list.
class IntList
	{
	Node *first;
	};
```

### Representation invariant

* `first` is iether null (empty list) or points to a valid `Node`
* In the last `Node`, `next` is always null (0)
* For every other `Node`, `next` points to another `Node`

### `IntList` Interface

``` cpp
class IntList
	{
	public:
		// EFFECTS: constructs an empty list
		IntList( );
		
		//EFFECTS: returns true if the list is empty
		bool IsEmpty( ) const;
		
		//REQUIRES: list is not empty
		//EFFECTS: Returns a reference to the first element
		// in the list
		int & Front( ) const;
		
		//EFFECTS: inserts datum into the front of the list
		void PushFront( int datum );
		
		//REQUIRES: list is not empty
		//EFFECTS: removes the item at the front of the list
		void PopFront( );
	};
```

### Information hiding

``` cpp
class IntList
	{
	private:
		struct Node
			{
			int datum;
			Node *next;
			};
		Node *first;
	};
```

No code outisde the class needs to know about the `Node` type, so it is made `private`

This is an example of *information hiding*

We're not giving this class a `Node` variable, but rather describing what a future `Node` variable would look like

### `IntList` constructor

``` cpp
class IntList
	{
	private:
		struct Node
			{
			int datum;
			Node *next;
			};
		Node *first;
	
	public:
		// EFFECTS: constructs an empty list
		IntList( ) : first( nullptr )
			{
			}
	};
```

### `IsEmpty( )`

``` cpp
class IntList
	{
	private:
		struct Node
			{
			int datum;
			Node *next;
			};
		Node *first;

	public:
		//EFFECTS: returns true if the list is empty
		bool IntList::IsEmpty( ) const
			{
			return first == nullptr;
			}
	};
```

### `Front( )`

``` cpp
class IntList
	{
	private:
		struct Node
			{
			int datum;
			Node *next;
			};
		Node *first;
	
	public:
		//REQUIRES: list is not empty
		//EFFECTS: Returns a reference to the first
		// element in the list
		int &Front( ) const
			{
			assert( !IsEmpty( ) );
			return first->datum;
			}
	};
```

#### Using `Front( )`

We can both inspect and modify the item inside a node with `Front( )` because it returns a reference

We can also use the reference variable `x`

``` cpp
int &Front( ) const
	{
	assert( !IsEmpty( ) );
	return first->datum;
	}

int main( )
	{
	IntList il;
	il.PushFront( 1 );
	int &x = il.Front( );
	cout << x << endl; // 1
	x = 17;
	cout << x << endl; // 17
	}
```

### `PushFront( )`

When we insert an integer, we start out with the `first` field pointing to the current list

The current list might be empty, or not

The first thing we need to do is to create a new node to hold the new first element

Then, we need to establish teh invariants on the new node by setting `datum` field to the new element and setting `next` field to the beginning of the current list

Lastly, we need to fix the representation invariant by resetting `first`

``` cpp
void IntList::PushFront( int datum )
	{
	Node *p = new Node;
	p->datum = datum;
	p->next = first;
	first = p;
	}
```
### `PopFront( )`

First, check the `REQUIRES` clause

If we are removing the front node, we must delete it to avoid a memory leak

Unofortunately, we can't delete it before advancing the `first` pointer

But, after we advance `first`, the removed node is an orphan, and can't be deleted

This is solved by using a local variable to remember the "old" first node, called `victim`

Store pointer in `victim`, then delte the node **after** `first` is updated

``` cpp
void IntList::PopFront( )
	{
	assert( !IsEmpty( ) );
	Node *victim = first;
	first = first->next;
	delete victim;
	}
```

### Traversing a linked list

We can use pointers to visit each node in a list

``` cpp
class IntList
	{
	public:
		void Print( ) const;
		//...
	};
	
void IntList::Print( ) const
	{
	for ( Node *i = first; i != nullptr; i = i->next )
		cout << i->datum << " ";
	}
```

### Big Three

* Destructor
	1. Remove all nodes -  `PopAll( )`
* Copy constructor
	1. Initialize member variables
	2. Copy all nodes from other list - `PushAll( )`
* Overloaded assignment operator
	1. Removed all nodes from this list - `PopAll( )`
	2. Copy all nodes from other list - `PushAll( )`

We need the Big Three, because `IntList` has a member variable that points to dynamic memory allocated by `IntList`

#### `PopAll( )`

We already have a funciton to remove one node, `PopFront( )`, let's reuse it

``` cpp
void IntList::PopAll( )
	{
	while ( !IsEmpty( ) )
		PopFront( );
	}
```

#### `PushAll( )`

What happens if we try to reuse `PushFront( )` to implement `PushAll( )`?

``` cpp
void IntList::PushAll( const IntList &other )
	{
	for ( Node *p = other.first; p != nullptr; p = p->next )
		PushFront( p->datum );
	}
```

We get a backwards copy!

We could easily write `PushAll( )` if we had a `PushBack( )` function

#### `PushBack( )`

With our current implementation, we would have to walk down the list to reach the last node and modify it's `next` pointer

This necessitates that we add a `last` pointer

``` cpp
class IntList
	{
	//...
	private:
		Node *first;
		Node *last;
	};
```

`last` points to the last node of the list if it is not empty, and `nullptr` otherwise

To implement `PushBack( )`, we first create a new node and establish its invariants

Then we handle the empty and non-empty list cases

``` cpp
void IntList::PushBack int datum )
	{
	Node *p = new Node;
	p->datum = datum;
	p->next = nullptr;
	if ( IsEmpty( ) )
		first = last = p;
	else
		last = last->next = p;
	}
```

Now that `PushBack( )` is finished, `PushAll( )` works too

``` cpp
void IntList::PushAll(const IntList &other )
	{
	for ( Node *p = other.first; p != nullptr; p = p->next )
		PushBack( p->datum );
	}
```

#### Destructor

``` cpp
void IntList::PopAll( )
	{
	while ( !IsEmpty( ) )
	PopFront( );
	}
	
void IntList::PopFront( )
	{
	assert( !IsEmpty( ) );
	Node *victim = first;
	first = first->next;
	delete victim;
	}
	
IntList::~IntList( )
	{
	PopAll( );
	}
```

#### Copy constructor

We need to do two things:

1. Initialize member varibles
2. Copy all nodes from other list

The assignment operator must do three things:

1. Remove all nodes form this list
2. Copy all nodes from other list
3. Ensure that there is no self assignment

``` cpp
IntList::IntList( const IntList &other ) :
	first( nullptr ), last( nullptr )
	{
	PushAll( other );
	}

void IntList::PushAll( const IntList &other )
	{
	for ( Node *p = other.first;
	p != nullptr; p = p->next )
	PushBack( p->datum );
	}

void IntList::PushBack( int datum )
	{
	Node *p = new Node;
	p->datum = datum;
	p->next = nullptr;
	if ( IsEmpty( ) )
		first = last = p;
	else
		{
		last->next = p;
		last = p;
		}
}

IntList & IntList::operator=( const IntList &rhs )
	{
	if ( this == &rhs )
		return *this;
	PopAll( );
	PushAll( rhs );
	return *this;
	}
```