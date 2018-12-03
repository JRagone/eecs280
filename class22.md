Class 22
========

## Review: lists and trees

### List

* An empty List
* A datum followed by a List

``` cpp
struct Node
	{
	int datum;
	Node *next;
	};
```

#### Print

``` cpp
//Using iteration
void PrintList( Node *n )
	{
	while ( n )
		{
		cout << n->datum << " ";
		n = n->next;
		}
	}
	
//Using recursion
void PrintList( Node *n )
	{
	if ( n )
		{
		cout << n->datum << " ";
		PrintList( n->next );
		}
	}
	
void PrintList( Node *n )
	{
	if ( n == nullptr )
		return;
	cout << n->datum << " ";
	PrintList ( n->next );
	}
```

`PrintList` is tail recursive, meaning that we can "re-use" the stack frame

### Tree

* An empty Tree
* A datum with left and right sub-trees

``` cpp
struct Node
	{
	int datum;
	Node *left,
		*right;
	};
```

#### Print

``` cpp
void PrintTree( Node *n )
	{
	if ( n )
		{
		PrintTree( n->left );
		cout << n->datum << " ";
		PrintTree ( n->right );
		}
	}
```

This is called an ***in-order*** traversal:

1. Visit left
2. Visit the root
3. Visit the right

## Traversals

### In-order

1. Visit left
2. Visit root
3. Visit right

### Preorder

1. Visit root
2. Visit left
3. Visit right

### Postorder

1. Visit left
2. Visit right
3. Visit root

## Binary search trees (BSTs)

### Properties

* It is empty

or

* All elements in any left subtree are less than the root and all elements in any right subtree are greater than the root

### Searching

If we're looking for an element in a BST, comparing with the root tells us where to look

If the element is less than the root, we search the left sub-tree

If the element is greater than the root, we search the right sub-tree

### BST `max`

The maximum element in a binary search tree is:

* The datum in the node if the right sub-tree is empty
* Otherwise, the maximum element in the right sub-tree

``` cpp
struct Node
	{
	int datum;
	Node *left;
	Node *right;
	};

// REQUIRES: 'n' is a non-empty BST
// EFFECTS: Returns the largest element
int TreeMax( Node *n )
	{
	// Base case: Right subtree is empty
	if ( !n->right )
		return n->datum;
	
	// Recursive case: Right subtree not empty
	return TreeMax( n->right );
	}
```

### BST `contains`

This solution is tail-recursive

``` cpp
struct Node
	{
	int datum;
	Node *left;
	Node *right;
	};

// REQUIRES: 'n' is a BST
// EFFECTS: Returns true if tree
// contains 'i'
bool TreeContains( Node *n, int i )
	{
	if ( n == nullptr )
		return false;
	if ( n->datum == i )
		return true;
	if ( i < n->datum )
		return TreeContains( n->left, i );
	return TreeContains( n->right, i );
	}
```

### BST shapes

Build a second BST with this
insertion order: 1, 2, 3, 4, 5, 6, 7

This is called a ***stick*** and it is ***unbalanced***

For any data and any weightin of frequency of access, there will always be a worst-performing arrangement of the data as a stick. This makes operations slow!

### In cpp

``` cpp
// A template for a simple binary tree.

template < typename D >
	struct Node
		{
		D datum;
		Node *left,
			*right;
		Node( D d ) : datum( d ), left( nullptr ), right( nullptr )
			{
			}
		~Node( )
			{
			delete left;
			delete right;
			}
		};
		
template < typename D >
	class Tree
		{
		public:
			Node< D > *Root;
			Tree( ) : Root( nullptr )
				{
				}
			~Tree( )
				{
				delete Root;
				}

			// In this variation on a Find routine, it can tell
			// you where it would have been attached if it's
			// not found.
			
			static Node< D > *Find( Node< D > *root, const D datum,
					Node< D > ***parentLink = nullptr )
				{
				if ( !root )
					return nullptr;
				if ( datum == root->datum )
					return root;
				if ( datum < root->datum )
					{
					if ( parentLink )
						*parentLink = &root->left;
					return Find( root->left, datum, parentLink );
					}
				if ( parentLink )
					*parentLink = &root->right;
				return Find( root->right, datum, parentLink );
				}
			
			Node< D > *Insert( D datum )
				{
				Node< D > **parent = &Root, *n;
				n = Find( Root, datum, &parent );
				if ( !n )
					n = *parent = new Node< D >( datum );
				return n;
				}
		};
```

### Traversals

``` cpp
// Traversals

void Inorder( Node< string > *n )
	{
	if ( n )
		{
		Inorder( n->left );
		cout << n->datum << endl;
		Inorder( n->right );
		}
	}

void Preorder( Node< string > *n )
	{
	if ( n )
		{
		cout << n->datum << endl;
		Preorder( n->left );
		Preorder( n->right );
		}
	}

void Postorder( Node< string > *n )
	{
	if ( n )
		{
		Postorder( n->left );
		Postorder( n->right );
		cout << n->datum << endl;
		}
	}
```

``` cpp
int main( int argc, char **argv )
	{
	string token;
	Tree< string > tree;
	
	// Build a tree of words read from cin.
	while ( cin >> token )
		tree.Insert( token );
		
	cout << "Inorder traversal:" << endl;
	Inorder( tree.Root );
	
	cout << endl << endl << "Preorder traversal:" << endl;
	Preorder( tree.Root );
	
	cout << endl << endl << "Postorder traversal:" << endl;
	Postorder( tree.Root );
	}
```

``` sh
g++ TreeTraversal.cpp -o TreeTraversal
TreeTraversal.exe
now is the time for all good men
```

Inorder traversal: all for good is men now the time

Preorder traversal: now is for all good men the time

Postorder traversal: all good for men is time the now

## `Set`

### Outline

``` cpp
template <typename T>
	class BSTSet
		{
		private:
			BinarySearchTree<T> elements;
		
		public:
			void insert( const T &v )
				{
				if ( !elements.contains( v ) )
				elements.insert( v );
				}
			bool contains( const T &v ) const
				{
				return elements.contains( v );
				}
			int size( ) const
				{
				return elements.size( );
				}
		};
```

### Efficiency

||UnsortedSet|SortedSet|BSTSet|
|---|---|---|---|
|**insert**|O( n )|O( n )|O( log n )|
|**remove**|O( n )|O( n )|O( log n )|
|**contains**|O( n )|O( log n )|O( log n )|
|**size**|O( 1 )|O( 1 )|O( n )|
|**constructor**|O( 1 )|O( 1 )|O( 1 )|

## `map`

Can one `BSTSet::Node` store two data?

Yes, this is called a `map` in C++

A `map` stores key/value pairs

A `map` is an *associative container*

You can implement a map using a BST

Each `Node` holds a `pair`

``` cpp
template <typename KeyType, typename ValueType>
	struct pair
		{
		KeyType first;
		ValueType second;
		}
```

### Example

Declare a map with two types: KeyType and ValueType

Each BST node contains a `pair` of `<string, int>`

`first` is a `string`, `second` is an `int`

Access map items using `operator[ ]`

The "index" doesn't have to be a number

``` cpp
map<string, int> chicken_count;

chicken_count[ "Marilyn" ] = 1;
chicken_count[ "Myrtle" ] = 2;

//Using an iterator

for ( map<string,int>::iterator
		i = chicken_count.begin( );
		i != chicken_count.end( ); ++i )
	cout << i->first << " " << i->second << "\n";

// Using a range-for

for ( auto i:chicken_count )
	cout << i.first << " " << i.second << endl;
```

### Word count

A map is good for a word count task, and this is common in machine learning and information retrieval

``` cpp
string document = "Myrtle Marilyn Maude Marilyn Mabel";
istringstream is( document );
map<string, int> word_count;
string word;

while ( is >> word )
	word_count[ word ] += 1;

// Print results
for ( auto i:word_count )
	cout << i.first << " " << i.second << "\n";
```

## Review: types of recursion

### Linear recursion

At most one recursive call each time the function is called

#### Example

`factorial`, `ListSize`, `ListMax`

### Tail recursion

Linear recursion and all recursive calls are tail calls

No work is done after a recursive call

#### Example

Versions of: `factorial`, `ListSize`, `list_max`

### Tree recursion

Multiple recursive calls from at least one function call

#### Example

`TreeSize`, `Tree_Height`

#### Fibonacci

A function doesn't have to operate on trees to be tree recursive

It is tree recursive if the structure of the recursive calls looks like a tree

``` cpp
int fib( int n )
	{
	if ( n <= 1 )
		return n;
	return fib( n - 1 ) + fib( n - 2 );
	}
```

## Solving problems with recursion

1. Think about how the problem can be broken down into similar, "smaller" problems
2. Put this idea into a more formal **recurrence relation**
3. Write code to implement a solution to the problem
	* Base case ( or cases )
	* Recursive case ( or cases )

### Pancake sort

Let's say you want to sort a stack of pancakes, and you have a spatula that can flip over any number of pancakes on top of the stack

1. Put spatula under largest pancake
2. Flip top portion, so the largest is now on top
3. Put spatula under all pancakes
4. Flip everything, so the largest is now on bottom
5. Repeat on smaller stack