EECS 280 - Class 2
==================

## Procedural Abstraction

*separates what a procedure does from how it is implemented*

* In cpp, we use functions to implement PA

* Can change implementation without affecting users as long as interface remains unchanged

.h header defines what, not how, functions do

.cpp defines how functions work
	•	functions can be used in another file

### Properties of PA

Code that calls the function depends on the ***interface***, not ***implementation***

As long as *abstraction* of a function does not change, usage of the function still works

#### Local

The implementation of an abstraction can be understood without examining any other abstraction implementation

#### Substitutable

You can replace one (correct) implementation of an abstraction with another (correct) one, and no callers of that abstraction will need to be modified

## Specification Comment

Signature describes inputs and outputs of function

Comments describe what function does

###	REQUIRES	 - Pre-conditions must hold

``` cpp
//REQUIRES: v is not empty
//EFFECTS: returns the sum of the numbers in v
double sum( std::vector<double> v );
```
* functions with REQUIRES clauses are called partial

* functions without REQUIRES clause are called complete

### MODIFIES - How inputs / global vars are modified

``` cpp
//MODIFIES: v
//EFFECTS: sorts v
void sort( std:: vector<double> &v );
```

* A function that modifies inputs or global data has **_side effect_**

### EFFECTS - What procedure does

## assert()

* Usage: `assert( expression )`;

* Does nothing if the expression is true

* Exits and prints error message if expression is false

``` cpp
#include <cassert>
int main( )
	{
	assert ( true ); // does nothing
	assert ( false ); // crash with debug message
	}
```

### Disabling asserts

```#define NDEBUG```

## Testing

### Goals of testing

1. Ensure that code works as expected
2. Meet the reqs of the spec

### Unit test

Test one piece, e.g., one function, at a time. Find and fix bugs early with smaller, less complex, easier to understand tests

Write small helper functions for tests

``` cpp
//stats_test.cpp
void test_mean( )
	{
	vector<double> data = { 1, 2, 3 };
	double result = mean( data );
	assert( result == 2 );
	}
```

#### Small Scope Hypothesis

*Thorough testing with "small" test cases is sufficient to find a large proportion of bugs within a system*

Can fail if...

1. Code coverage is incomplete
2. Cannot detect bugs that occur as the result of interactions adn race conditions that it doesn't (or cannot) test

### System test

Test the entire system after all the individually-tested pieces have been integrated

### Regression test

Automatically run all unit and system tests after a code change

`make test` for running regression tests

### Test-driven development

1. Code tests
2. Code implementation with the goal of passing the tests
3. Debug and add more tests
