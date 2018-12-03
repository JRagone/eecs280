Class 23
========

## Containers of values

Containers use templates, so they can hold any type, including custom types created with the class mechanism

``` cpp
class Gorilla
	{
		//...
	public:
		~Gorilla( )
			{
			cout << "Gorilla dtor: " < name << "\n";
			}
		
		Gorilla( const Gorilla &other )
			{
			name = other.name + " clone";
			cout << "Gorilla copy ctor: " << name << "\n";
			}
		
		Gorilla &operator=( const Gorilla &rhs )
			{
			name = rhs.name + " clone";
			cout << "Gorilla operator=: " << name << "\n";
			return *this;
			}
	};
```

How many constructors run below?

``` cpp
struct Node
	{
	Node *next;
	T datum;
	};

void List< T >::PushFront( T datum )
	{
	Node *p = new Node;
	p->datum = datum;
	p->next = front;
	front = p;
	}

class Gorilla
	{
	// A big, expensive class ...
	};

int main( )
	{
	List< Gorilla > zoo;
	zoo.PushFront( Gorilla( "Colo" ) );
	}
```

``` text
Gorilla ctor: Colo
Gorilla copy ctor: Colo clone
Gorilla default ctor
Gorilla operator=: Colo clone
clone Gorilla dtor: Colo clone
Gorilla dtor: Colo
Gorilla dtor: Colo clone clone
```

We can fix the first copy by passing by reference

``` cpp
void List< T >::PushFront( T &datum )
	{
	Node *p = new Node;
	p->datum = datum;
	p->next = front;
	front = p;
	}
```

The second one is harder to fix inside the list template

A better solution is to use the list as a ***container of pointers***

``` cpp
int main( )
	{
	List< Gorilla * > zoo;
	zoo.PushFront( new Gorilla( "Colo" ) );
	}
```

### Motivation

1. Avoid slow copies of big objects
2. Allows you to manage the lifetime of the variable yourself
3. Allows you to create unique objects shared on multiple lists
4. Useful when many parts of the code refer to an object, but you only want one copy in memory

### Example

Francine says hi to each animal by name as she feeds it, e.g., "Hi, Colo", and then removes it from her todo list

``` cpp
int main( )
	{
	List< Gorilla * > zoo;
	zoo.PushFront( new Gorilla( "Colo" ) );
	zoo.PushFront( new Gorilla( "Koko" ) );
	List< Gorilla * > todo = zoo;
	
	while ( !todo.Empty( ) )
		{
		Gorilla *g = todo.Front( );
		cout << "Hi, " << g->GetName( ) << "\n";
		todo.PopFront( );
		}
	}
```

Problem: The list destructor removes the `Node` objects, but not the `Gorilla` objects. Thus, there are orphan `Gorilla`s on the heap

#### A bad solution

``` cpp
List< T >::PopFront( )
	{
	assert( !Empty( ) );
	Node *victim = front;
	front = front->next;
	delete victim->datum;
	delete victim;
	}

int main( )
	{
	List< int > li;
	li.PushFront( 7 );
	}
```

This won't compile, because `PopFront( )` tries to delete an object that is not a pointer

And this breaks with a dangling pointer

``` cpp
List< T >::PopFront( )
	{
	assert( !Empty( ) );
	Node *victim = front;
	front = front->next;
	delete victim->datum;
	delete victim;
	}

int main( )
	{
	List< Gorilla * > zoo;
	zoo.PushFront( new Gorilla( "Colo" ) );
	Gorilla *g = zoo.Front( );
	zoo.PopFront( );
	g->GetName( );
	}
```

This could be fixed by adding

``` cpp
int main( )
	{
	List< Gorilla * > zoo;
	zoo.PushFront( new Gorilla( "Colo" ) );
	zoo.PushFront( new Gorilla( "Koko" ) );
	
	List< Gorilla * > todo = zoo;
	while ( !todo.Empty( ) )
		{
		Gorilla *g = todo.Front( );
		cout << "Hi, " << g->GetName( ) << "\n";
		todo.PopFront( );
		}
	
	for ( auto i:zoo )
		delete i; //fixed
	}
```

#### Another problem

``` cpp
int main( )
	{
	List< Gorilla * > zoo;
	zoo.PushFront( new Gorilla( "Colo" ) );
	zoo.PushFront( new Gorilla( "Koko" ) );
	
	List< Gorilla * > todo = zoo;
	
	// Koko moves to another zoo
	delete zoo.Front( );
	zoo.PopFront( );
	
	// Francine starts feeding Gorillas ...
	...
	}
```

Francine will try to feed koko, not knowing that he's not here any more

### Pattern of use for a container of pointers

Containers-of-pointers are subject to two broad kinds of bugs:

1. Dangling pointers: using an object after is has been deleted
2. Memory leaks: leaving an object orphaned by never deleting it

Containers do not manage memory outside of themselves. The container doesn't know what you want to do

Pattern of use for avoiding these bugs:

1. Whoever creates memory is responsible for deleting it
2. Every time you create memeory with `new`, you must remove it with `delete`
3. This is called ***ownership***

### Disabling copy

Somtimes, it's helpful to disable copying if you truly don't *ever* want the objects copied, not even by member functions

``` cpp
class Gorilla
	{
	//...
	private:
		Gorilla( const Gorilla &other );
		Gorilla &operator=( const Gorilla &rhs )
			= delete;
	};
```

### Motivation continued

5. ***Also useful for polymorphism***

## Containers of polymorphic types

Let's make our zoo work with many kinds of animals

***Abstract*** base class `Animal`

***Derived*** types `Gorilla` and `Zebra`

``` cpp
class Animal
	{
		string name;
	public:
		Animal( string name ) : name( name )
			{
			}
		virtual string GetName( ) const
			{
			return name;
			}
		virtual string says( ) const = 0;
	};

class Gorilla : public Animal
	{
	public:
		Gorilla( string name ) : Animal( name )
			{
			}
		virtual string says( ) const
			{
			return "bananas!";
			}
	};

class Zebra : public Animal
	{
	public:
		Zebra( string name ) : Animal( name )
			{
			}
		virtual string says( ) const
			{
			return "stripes!";
			}
	};
```

The `virtual` keyword is optional in the derived class

``` cpp
int main( )
	{
	List< Animal * > zoo;
	
	zoo.PushFront( new Gorilla( "Colo" ) );
	zoo.PushFront( new Gorilla( "Koko" ) );
	zoo.PushFront( new Zebra( "Zebo" ) );
	
	for ( auto i:zoo )
		cout << i->GetName( ) << " says " <<
			i->says( ) << endl;
	}
```

Now, `zoo` is a container of pointers to polymorphic types

### Destructors and polymorphism

When you create an object, all the contructors run, starting with the base class

Now, let's see what happens when we mix destructors with polymorphism

If we add print messages to `Animal`, `Gorilla` and `Zebra` constructors and destructors, what will the output be?

``` cpp
class Animal
	{
	public:
		Animal( string name ) : name( name )
			{
			cout << "Animal ctor\n";
			}
		~Animal( )
			{
			cout << "Animal dtor\n";
			}
	//...
	};
```

``` text
Animal ctor
Gorilla ctor
Animal ctor
Gorilla ctor
Animal ctor
Zebra ctor
Zebo says stripes!
Koko says bananas!
Colo says bananas!
```

However, this code leakes memory. Since we allocated the animals, must free them

``` cpp
int main( )
	{
	List< Animal * > zoo;
	
	zoo.PushFront( new Gorilla( "Colo" ) );
	zoo.PushFront( new Gorilla( "Koko" ) );
	zoo.PushFront( new Zebra( "Zebo" ) );
	
	for ( auto i:zoo )
		cout << i->GetName( ) << " says "
			<< i->says( ) << endl;
	
	for ( auto i:zoo )
		delete i;
	}
```

``` text
Animal ctor
Gorilla ctor
Animal ctor
Gorilla ctor
Animal ctor
Zebra ctor
Zebo says stripes!
Koko says bananas!
Colo says bananas!
Zebra dtor
Animal dtor
Gorilla dtor
Animal dtor
Gorilla dtor
Animal dtor
```

### Virtual destructors

Polmorphic objects need virtual destructors

If you have a virtual function and a destructor, then the destructor probably needs to be virtual too

``` cpp
class Animal
	{
	public:
		Animal( string name ) : name( name )
			{
			cout << "Animal ctor\n";
			}
		virtual ~Animal( )
			{
			cout << "Animal dtor\n";
			}
	//...
	};
```