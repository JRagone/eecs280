Class 20
========

## Stack Review

``` cpp
int plus_one( int x )
	{
	return x + 1;
	}

int plus_two( int x )
	{
	return 1 + plus_one( x );
	}

int main( )
	{
	int result = 0;
	result = plus_one( 0 ); // result = 1
	result = plus_two( result ); // result = 3
	cout << result; //3
	return 0;
	}
```

## Recursion

Functions can call other functions, and a function can call itself

The below is an example of a function calling itself, but it is missing some code that prevents it from crashing

``` cpp
void countToInfinity( int x )
	{
	cout << x << endl;
	countToInfinity( x + 1 );
	}

int main( )
	{
	countToInfinity( 0 );
	}
```

### Solving problems with recursion

Two features of problems that make recursion an attractive solution:

1. ***Subproblems that are similar or "smaller"*** and closer to a base case
2. A ***base case*** that can be ***solved without recursion***

``` cpp
void countToTen( int x )
	{
	cout << x << endl;
	if ( x == 10 )
		return;
	countToTen( x + 1 );
	}

int main( )
	{
	countToTen( 0 );
	}
```

### Base case

`x == 10` is the base case. When this evaluates to be true, the function does not call itself again, and the recursion stops

### Recursive step

`countToTen( x + 1 )` is the recursive step, and this brings `x` closer to the base case. If it brought `x` farther from the base case, we would be in jeopardy of looping forever

### Exercise: factorial

``` cpp
// REQUIRES: n >= 0
// EFFECTS: computes and
// returns n!

int factorial( int n )
	{
	if ( n == 0 )
		// Base case
		return 1;
		
	// Recursive case
	return n * factorial( n - 1 );
	}
```

``` cpp
// REQUIRES: n >= 0
// EFFECTS: computes and
// returns n!

int factorial( int n )
	{
	return n == 0 ?
		// Base case
		1 :
		// Recursive case
		n * factorial( n - 1 );
	}
```

### Recursion and the stack

``` cpp
int main( )
	{
	int n;
	cin >> n;
	int x = factorial( n );
	}
```

How many multiplications? **n** ***(linear time)***

How many stack frames maximum? **n + 1** ***(linear space)*** not counting `main( )`

This gives us a rough measure of how much memory is used by `factorial`

#### An iterative version

``` cpp
int factorial( int n )
	{
	int result = 1;
	for ( ; n > 0; n-- )
		result *= n;
	return result;
	}
```

How many multiplications? **n** ***(linear time)***

How many stack frames (max)? **1** ***(constant space)***

Constant space means that even when `n` gets bigger, we still need only 1 stack frame

### Recursion vs. Iteration

#### Recursion

1. Computation is done **after** the "repetition"
2. Multiplication happens during **passive flow**
3. We need to keep track of each stack frame with each value of `n`

#### Iterative

1. Computation is done **before** the "repetition"
2. Multiplication happens in **active flow**. There is no passive flow in iteration
3. Once a value of `n` is multiplied into `result`, we can just forget it!

### Is all recursion like this?

Does recursion always do work in the passive flow? No.

### Tail calls

A function call is a ***tail call*** if it is the very last thing in its containing function

The calling function has ***no pending work*** to do after a tail call (in the passive flow)

Avoids saving the stack frame!

We can rewrite a tail recursive call into an update to the local variables and a return to the top

``` cpp
void countToTen( int x )
	{
	cout << x << endl;
	if (x == 10)
		return;
	countToTen( x + 1 );
	//no more work to do
	}

void countToTen( int x )
	{
	while (true )
		{
		cout << x << endl;
		if (x == 10)
			return;
		x = x + 1;
		}
	}
```

#### Tail call optimization

1. Most compilers are able to recognize tail calls and optimize them, reusing the current stack frame for a new recursive call
2. With g++, the -O2 and -O3 options include tail call optimization
3. Tail call optimization can take a recursive algorithm from linear to constant space complexity as long as it only makes tail calls

#### Tail recursion

We say a function is ***tail recursive*** if ALL the recursive calls it makes are tail calls

``` cpp
//REQUIRES: x >= 1
//EFFECTS: returns floor of
// base 2 logarithm of x

int log2( int x )
	{
	if (x == 1)
		return 0; // Base case
		
	// Recursive case
	return 1 + log2( x/2 );
	
	// Not a tail call because it
	// does work (adding 1) after
	// the return.
	}
```

#### Tail-recursion identification

``` cpp
void countdown1( int n )
	{
	if ( n <= 0 )
		return;
	else
		{
		cout << n << endl;
		countdown1( n – 1 );
		return;
		}
	}
```

The above is tail-recursive, because **it doesn't do any work after the recursive call returns**

We can rewrite the tail-recursion

``` cpp
void countdown1( int n )
	{
	while ( n > 0 )
		{
		cout << n << endl;
		n = n - 1;
		}
	}
```

``` cpp
void countdown1( int n )
	{
	if ( n <= 0 )
		return;
	else
		{
		cout << n << endl;
		countdown1( n – 1 );
		return;
		}
	}

void countdown2( int n )
	{
	if ( n <= 0 )
		return;
	cout << n << endl;
	countdown2( n – 1 );
	}

void countdown3( int n )
	{
	if ( n > 0 )
		{
		cout << n << endl;
		countdown3( n – 1 );
		}
	}
```

The above is tail-recursive

``` cpp
void countdown5( int n )
	{
	if ( n <= 0 )
		return;
	cout << n << endl;
	if ( n % 2 )
		{ // n is odd
		countdown5( n – 1 );
		return;
		}
	else
		{ // n is even
		cout << n - 1 << endl;
		countdown5( n – 2 );
		return;
		}
	}
```

The above is tail-recursive. It's OK to have two recursive calls, as long as there is no pending computation after the call

#### Tail recursive factorial

``` cpp
// Possible attempted tail-
// recursive solution
int factorial( int n )
	{
	int result = 1;
	if ( n == 0 )
		return result;
	result *= n;
	return factorial( n – 1 );
	}
```

***What's wrong with this tail recursive version?***

We get a new version of `result` whose value is 1 in every new stack frame, so `result` never grows

This isn't a problem in the iterative version, because there's only one stack frame and only one `result` variable

##### Solution

Make `result` a parameter

``` cpp
// Tail recursive solution
int factorial( int n, int result )
	{
	if ( n == 0 )
		return result;
	result *= n;
	return factorial( n – 1, result );
	}
```

There is still a separate `result` object for each call (stack frame), but we can pass the updated value along to the next

##### Rewritten iterative factorial

``` cpp
// Rewriting
int factorial( int n, int result )
	{
	while ( n > 0 )
		{
		result *= n;
		n = n – 1;
		}
	return result;
	}
```

But wait... we broke the procedural abstraction by changing the function signature.

Solution: Use a ***helper function*** to fix the procedural abstraction

``` cpp
int factorialHelper( int n,
		int result )
	{
	if ( n == 0 )
		return result;
	return factorialHelper( n-1, n*result );
	}

int factorial( int n )
	{
	return factorialHelper( n, 1 );
	}
```
#### Tail-recursion identification

``` cpp
void countdown4_help(int n, int i)
	{
	if ( i > n )
		return;
	countdown4_help( n, i + 1 );
	cout << i << endl;
	}

void countdown4( int n )
	{
	countdown4_help( n, 1 );
	}
```

***Not tail-recursive***. A helper function does not always make a tail-recursive function!

### Recursion

Any recursive routine can be rewritten as an iterative routine with a stack

The existance proof is that the compiler can do it using the call stack
