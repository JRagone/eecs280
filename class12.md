Class 12
========

## Review

### Containers

The purpose of a ***container*** is to hold other objects

``` cpp
Set< int > is;
Set< string > chickens;
```

We used ***generic programming*** to write the algorithm and specify the type later

### Templates

``` cpp
template < typename T >
	class Set
	{
	public:
		Set( );
		void insert( T v );
		void remove( T v );
		bool query( T v ) const;
		int size( ) const;
		static const int NumberAllocated = 100;
	private:
		T elements[ NumberAllocated ];
		int numberOfElements;
		int index_of( T v ) const;
};
```

***Declare Set using T instead of `int`***

## Set efficiency

``` cpp
template < typename T >
	int Set< T >::index_of( T v ) const
		{
		for ( int i = 0; i < numberOfElements; ++i )
			if ( elements[ i ] == v )
				return i;
		return NumberAllocated;
		}
```

How many elements must `index_of` examine in teh worst case if there are 10 elements? It must examine 10 elements.

* We say the time for index_of grows linearly with the size of the set.

* If there are N elements in the set, we have to examine all N of them in the worst case. For large sets that perform lots of queries, this might be too expensive.

* Luckily, we can replace this implementation with a different one that can be more efficient. The only change we need to make is to the representation – the abstraction can stay precisely the same.

### Improving set efficiency

We can improve our set efficiency by ***keeeping elements in sorted order***

We have to change `remove( )` to "squish" the array

``` cpp
void Set< T >::remove( T v )
	{
	int gap = index_of( v );
	if ( gap == NumberAllocated )
		return; //not found
		
	--numberOfElements; //one less element
	
	while ( gap < numberOfElements )
		{
		//there are elements to our right
		elements[ gap ] = elements[ gap + 1 ];
		++gap;
		}
	}
```

We also have to change `insert( )` to place a new element in its sorted position

``` cpp
template < typename T >
	void Set< T >::insert( T v )
	{
	if ( index_of( v ) != NumberAllocated )
		return;//present
	assert( numberOfElements < NumberAllocated ); //REQUIRES
	int cand = numberOfElements - 1; // largest element
	while ( ( cand >= 0 ) && elements[ cand ] > v )
		{
		elements[ cand + 1 ] = elements[ cand ];
		--cand;
		}
	// Now, cand points to the left of the "gap"
	elements[ cand + 1 ] = v;
	++numberOfElements; // repair invariant
	}
```

We can improve `index_of( )`

* Compare foo against the ***middle*** element of the array and there are three possibilities:
	1. foo = the middle element.
	2. foo < the middle element.
	3. foo > the middle element.
* ***The comparison with the middle element eliminates at least half of the array from consideration!*** Then, we repeat the same thing over again.

A nonempty array has at least one element in it, so right >= left

``` cpp
int Set::index_of( T v ) const
	{
	int left = 0,
		right = numberOfElements - 1;
	while ( right >= left )
		{
		int size = right - left + 1,
			middle = left + size/2;
		if ( elements[ mi`ddle ] == v )
			return middle;
		if ( elements[ middle ] < v )
			left = middle + 1;
		else
			right = middle - 1;
		}
	return NumberAllocated;
	}
```

If this array has ***N*** elements, you'll need "about" ***log base 2 (N)*** comparisions to search it

We only need 7 comparisions in the worst case with 100 elements. This is called a ***binary search***

`insert` and `remove` are still linear O(N), but `query( )` becomes O(log N)

### Abstraction

We do not need to modify our `main()` program, becuase of data abstraction. Our interface remains unchanged.

### `using`

Now we have two files, `UnorderedSet.h` and `OrderedSet.h`

``` cpp
#include "UnorderedSet.h"
#include "OrderedSet.h"

//Select one implementation
template < typename T >
	using Set = OrderedSet< T >;
	
int main( )
	{
	Set< int > is;
	//...
	
	Set< string > chickens;
	//...
	}
```

## Static vs. dynamic polymorphism

* We could have implemented these two ADTs using ***dynamic polymorphism*** (`virtual` functions)

``` cpp
class Set { /*...*/ };
class UnorderedSet : public Set { /*...*/ };
class OrderedSet : public Set { /*...*/ };
```

We used ***static polymorphism***, because the type didn't change at run time

## Representation invariant

### Review

* An invariant is something that is always true
* A representation invariant applies to the data members of an ADT
	* Recall that member variables are a class's representation
* A representation invariant must be true immediately before and immediately after any member function execution
* Think of it as a "sanity check" for the ADT

### Check

#### `UnorderedSet`

The first `numberOfElements` members of `elements` contain the integers comprising the set, with no duplicates

``` cpp
bool UnorderedSet::check_invariant( )
	{
	for ( int i = 0; i < numberOfElements; ++i )
		for ( int j = i + 1; j < numberOfElements; ++j )
			if ( elements[ i ] == elements[ j ] )
				return false;
	return true;
	}
```

The nested loop in `check_invariant()` might be slow, and you may wish to diable the assert for production code

You can disable the assert the following ways:

1. Add a line of code `#define NDEBUG`
2. Sepcify it on the command line of the compiler `g++ -DNDEBUG ...`
3. Disable only that check

#### `OrderedSet`

The first `numberOfElements` members of `elements` contain the integers comprising the set, ***from lowest to highest***, with no duplicates

``` cpp
template < typename T >
	bool OrderedSet< T >::check_invariant( ) const
		{
		for ( int i = 0; i < numberOfElements - 1; ++i )
			if ( elements[ i ] >= elements[ i + 1 ] )
				return false;
		return true;
		}
```

Use `assert( )` with `check_invariant( )` to ensure that the representation invariant is true

``` cpp
#include <cassert>
template <typename T>
	void OrderedSet< T >::insert( T v )
		{
		assert( check_invariant( ) );
		//...
		}
```