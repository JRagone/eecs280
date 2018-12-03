Class 24
========

## Detecting errors at runtime

1. We want a way to detect and correct errors at runtime
2. Many times, the author of a library (or other code module) can detect errors, but doesn't know how to correct them
3. Today, we will use ***exceptions*** to separate ***error detection*** and ***error correction*** into two parts of a program

### Motivation

Recall our factorial function

``` cpp
//REQUIRES: n >= 0
//EFFECTS: returns n!

int factorial ( int n )
	{
	int result = 1;
	while ( n > 0 )
		{
		result *= n;
		n--;
		}
	return result;
	}
```

If we're asking the user for input, it would be easy to "accidentally" pass a negative value to factorial

Instead of the REQUIRES clause, let's look at another way of ensuring correct inputs: ***runtime checking***

If we can't guarantee formally (via a specification) that the inputs are correct, maybe we can guarantee this by checking the inputs explicitly before using them in our program

### Determining legitimate output for illegitimate input

There are three general strategies for determining legitimate output for illegitimate input:

1. "It's my problem!"
2. "I give up!"
3. "It's your problem!"

#### "It's my problem"

Try to "fix" things and continue execution by "coercing" legitimate inputs from illegitimate ones by either ***modifying them*** or ***returning default outputs***

However, factorial is undefined for negative numbers, and trying to define it changes the rules of math. It's not reasonable for a negative input to produce a legitimate output

#### "I Give up!"

Use `abort( )` or `exit( )`

``` cpp
//REQUIRES: n >= 0
//EFFECTS: returns n! for
//non-negative inputs and
//crashes the program for
//negative inputs

int factorial ( int n )
	{
	if ( n < 0 )
		exit( EXIT_FAILURE );
	// ...
	}
```

It is ***not nice*** to crash the program

What if there were open files? They could become corrupted

Exiting from a function deep in the call stack is (usually) not the way to handle an error!

#### "It's your problem!"

Sometimes, the code that detects the error does not know how to correct the error

One way to solve this is to encode failure in the return value

``` cpp
//EFFECTS: returns n! for
//non-negative inputs and
//returns a negative number
//for negative inputs.

int factorial ( int n )
	{
	if ( n < 0 )
		return n;
	// ...
	}
```

##### Problems

1. We're still changing the rules of math
2. Code that uses `factorial( )` forgets to check

``` cpp
int main( )
	{
	string f; //function
	int n; //number
	while ( cin >> f >> n )
		if ( f == "factorial" )
			cout << factorial( n ) << endl;
		else
			cout << "try again" << endl;
	}
```

3. Code that uses `factorial( )` gets messy

``` cpp
int main( )
	{
	string f; //function
	int n; //number
	while ( cin >> f >> n )
		if ( f == "factorial" )
			{
			int result = factorial( n );
			if ( result < 0 )
				cout << "try again" << endl;
			else
				cout << result;
			}
		else
			cout << "try again" << endl;
	}
```

4. Sometimes you can't encode "failure" elegantly in the return values

``` cpp
//EFFECTS: returns n^3

int cube( int n )
	{
	return n * n * n;
	}
```

## C++ Exceptions

***Exceptions*** let us detect an error in one part of the program and correct it in a different part of the program

For example, we could detect an error in `factorial( )` and correct it in `main( )`

Two meanings:

1. ***In C++***, an exception is something you throw, then catch higher up the call stack. You can't catch anything you didn't throw
2. ***To the operating system***, it's a hardware interrupt due to a null pointer deref (a seg fault), a floating point exception, ^C typed at the keyboard, etc.

### Exception propagation

When an exception occurs, it ***propagates*** from a function to its caler until it reaches a handler

You can imagine exceptions as a multi-level return

### Exception handling in C++

1. When code detects an error, it uses a **throw** statement
2. Code that might cause an error goes in a **`try{}`** block
3. Code that corrects an error goes in a **`catch{}`** block
4. If there is no code that corrects the error, the program exits

#### Terminology

1. You ***throw*** or ***raise*** an exception
2. You use a ***catch block***, also called an ***exception handler***, to deal with it

When code detects an error, it uses a **throw** statment

***Exceptions have types and values*** just like variables

You can think of this value as being a kind of parameter to the exception

``` cpp
int n = 0;
throw n;

char c = 'e';
throw c;
```

### Exception example

When code detects an error, it uses a **`throw`** statement

``` cpp
//EFFECTS: returns n!, throws n
//for negative inputs

int factorial ( int n )
	{
	if ( n < 0 )
	t	hrow n;
	int result = 1;
	while ( n > 0 )
		{
		result *= n;
		n--;
		}
	return result;
	}
```

If `n` is non-negative, no exception is thrown and the function returns its result

If `n` is negative:

1. No more code from this function executes
2. Control passes "up the chain" to the caller

Code that might cause an error goes in a **`try{}`** block

Code that corrects an error goes in a **`catch{}`** block, goes immediately after the `try{}` block

A **`catch{}`** block matches the type from a throw statement

***curly braces are required***

``` cpp
int main( )
	{
	string f; //function
	int n; //number
	while ( cin >> f >> n )
		try
			{
			if ( f == "factorial" )
				cout << factorial( n ) << endl;
			}
		catch( int i )
			{
			cout << "try again" << endl;
			}
	}
```

### Exercise

Write a combination function: n!/(k!(n-k)!)

``` cpp
//EFFECTS: returns n choose k,
// throws an exception for
// negative input

int combination( int n, int k )
	{
	return factorial( n ) /
		( factorial( k ) * factorial( n – k );
	}
```

No need to throw an exception because factorial already checks for negative inputs

Exceptions propagate to the caller

### Unhandled exceptions

When an exception is not caught by a catch block, it propagates all the way to the caller of `main( )`, and the program exits

``` sh
./a.out
combination -5 4
terminate called after
throwing an instance of
'int'
Aborted (core dumped)
```

### Type discrimination

A `try{}` block can have multiple `catch{}` blocks to handle different exception types

But they must all be one right after the other

``` cpp
try
	{
	if ( foo )
		throw 4;
	// some statements go here
	if ( bar )
		throw 2.0;
	// more statements go here
	if ( baz )
		throw 'a';
	}
catch ( int n )
	{ }
catch ( double d )
	{ }
catch ( char c )
	{ }
catch ( ... )
	{ }
```

The type of the thrown exception is matched, in order, to the list of catch blocks. The first matching catch block is executed

The last handler is a ***default handler*** which matches any exception type. It can be used as a "catch-all" in case no other catch block matches

The `...` means ***match anything***

### Exception types

Code often uses custom types to describe errors

We use the class mechanism to declare custom types

``` cpp
class NegativeError
	{
	//...
	};

class InputError
	{
	//...
	};
	
//EFFECTS: returns n!, throws
//NegativeError for n < 0

int factorial ( int n )
	{
	if ( n < 0 )
		throw NegativeError( );
	int result = 1;
	while ( n > 0 )
		{
		result *= n;
		n--;
		}
	return result;
	}
```

To correct an error, the `catch{}` block matches the type

``` cpp
int main( )
	{
	//...
	while ( cin >> f >> n )
		{
		try
			{
			//...
			}
		catch ( NegativeError n )
			{
			cout << "try a positive number“
			<< endl;
			}
		catch ( ... )
			{
			cout << "try again" <<endl;
			}
		}
	}
```

### STL routines use exceptions

``` cpp
#include <iostream>
#include <string>
using namespace std;

int main( int argc, char **argv )
	{
	string s;
	while ( cin >> s )
		{
		try
			{
			cout << "stoi( " << s << " ) = " << stoi( s ) << endl;
			}
		catch ( invalid_argument )
			{
			cout << "stoi( " << s << " ) is invalid" << endl;
			}
		catch ( out_of_range )
			{
			cout << "stoi( " << s << " ) is out of range" << endl;
			}
		}
	}
```

### Cannot catch hardware exceptions

There is no way to catch a hardware exceptions, such as "trycatch.exe has stopped working"

``` cpp
#include <iostream>
#include <cassert>
#include <stdexcept>
using namespace std;

int main( void )
	{
	int *p = 0;
	try
		{
		*p = 5;
		}
	catch ( ... )
		{
		cout << "Null pointer encountered" << endl;
		}
	}
```

The below code works, however

``` cpp
#include <iostream>
using namespace std;

int main( void )
	{
	int *p = 0;
	struct NullPointerError
		{
		int **BadPointer;
		NullPointerError( int **p )
			{
			BadPointer = p;
			}
		};
	
	try
		{
		if ( !p )
			throw NullPointerError( &p );
		*p = 5;
		}
	catch ( NullPointerError &e )
		{
		cout << "Null pointer at " << e.BadPointer <<" encountered" << endl;
		}
	}
```

We protect a possible null pointer deref with a test and a throw, but it's not realistic to do for every pointer deref, even if we could hide it behind a functor, esepecially as we expect this error should never happen

But the ***operating system*** catches null pointer derefs as a ***hardware exception*** at zero runtime cost for valid pointers

### Hardware exceptions

1. Problems like null pointer derefs are picked up by the processor as hardware exceptions
2. When they occur, the operating system gets a hardware interrupt, causing it to run a recovery routine, which usually results in terminating your program
3. You can ask the operating sytem to let you decide what to do and for you to do your own recovery

``` cpp
// tryexcept.cpp

// Simple Windows __try/__except example, intercepting
// a null pointer dereference using Microsoft try-except
// extension to C and C++. Compile with /EHsc.

// Choices for the __except filter expression value are:
//
// EXCEPTION_CONTINUE_EXECUTION = -1
// EXCEPTION_CONTINUE_SEARCH = 0
// EXCEPTION_EXECUTE_HANDLER = 1

// Nicole Hamilton nham@umich.edu

#include <windows.h>
#include <iostream>
#include <cassert>
#include <stdexcept>
using namespace std;

int main( void )
	{
	int *p = nullptr;
	
	__try
		{
		*p = 5;
		}

	__except ( EXCEPTION_EXECUTE_HANDLER ) // Intercept anything
		{
		cout << "Null pointer dereference encountered" << endl;
		}
	}
```

``` sh
175 C% cl /EHsc tryexcept.cpp
Microsoft (R) C/C++ Optimizing Compiler Version 19.11.25508.2 for x64
Copyright (C) Microsoft Corporation. All rights reserved.

tryexcept.cpp
Microsoft (R) Incremental Linker Version 14.11.25508.2
Copyright (C) Microsoft Corporation. All rights reserved.

/out:tryexcept.exe
tryexcept.obj
176 C% tryexcept.exe
Null pointer dereference encountered
177 C%
```

``` cpp
// tryexceptfilter.cpp

// A second Windows try/except example, intercepting
// a null pointer dereference using Microsoft try-except
// extension to C and C++ with an exception filter.
//
// Must be compiled with Microsoft Visual C++ /EHsc options.

// Nicole Hamilton nham@umich.edu

#include <windows.h>
#include <iostream>
#include <cassert>
#include <stdexcept>
using namespace std;

int ExceptionFilter( DWORD exceptionCode,
	EXCEPTION_POINTERS *exceptionPointers )
	{
	cerr << "Exception code = " << hex << exceptionCode << endl;
	return EXCEPTION_EXECUTE_HANDLER;
	}

int main( void )
	{
	// In this example, we'll filter the exception, using
	// GetExceptionCode and GetExceptionInformation to
	// retrieve information about what happened.
	//
	// GetExceptionCode and GetExceptioninformation can
	// only be called from within the __except expression,
	// not from a subroutine.
	int *p = nullptr;
	__try
		{
		*p = 5;
		}
	__except ( ExceptionFilter( GetExceptionCode( ),
			GetExceptionInformation( ) ) )
		{
		cout << "Null pointer dereference encountered" << endl;
		}
	}
```

``` sh
157 C% cl /EHsc tryexceptfilter.cpp
Microsoft (R) C/C++ Optimizing Compiler Version 19.11.25508.2 for x64
Copyright (C) Microsoft Corporation. All rights reserved.

tryexceptfilter.cpp
Microsoft (R) Incremental Linker Version 14.11.25508.2
Copyright (C) Microsoft Corporation. All rights reserved.

/out:tryexceptfilter.exe
tryexceptfilter.obj
158 C% tryexceptfilter.exe
Exception code = c0000005
Null pointer dereference encountered
```

``` cpp
// LinuxSignalHandler.cpp

// Simple demonstration of how to use the Linux sigaction system call
// along with setjmp and longjmp to intercept a segfault (or any other
// exception.)

// Nicole Hamilton nham@umich.edu

#include <unistd.h>
#include <setjmp.h>
#include <string.h>
#include <iostream>
using namespace std;

bool errorEncountered = false;
jmp_buf longjmpBuffer;

void SignalHandler( int signal, siginfo_t *info, void *context )
	{
	cerr << "Signal " << signal << " encountered." << endl;
	
	// Indicate that an error occurred so we don't go down
	// the same path again when we return.
	
	errorEncountered = true;
	
	// longjmp back to just before we entered the block
	// failed, returning the signal number.
	
	longjmp( longjmpBuffer, signal );
	}

int main( int argc, char **argv )
	{
	// Create a sigaction structure describing how we'd
	// like to intercept signals.
	
	struct sigaction action;
	memset( &action, 0, sizeof( action ) );
	action.sa_sigaction = SignalHandler;
	action.sa_flags = SA_SIGINFO;
	
	// Install the signal handler for seg faults. (We have
	// do this for each signal we'd like to intercept.)
	
	int sigactionResult = sigaction( SIGSEGV, &action, nullptr );
	
	// Before entering suspect code, capture a convenient
	// spot to return to. If we encounter an error, this
	// is where we'll pop out again. (Think Groundhog Day
	// or Butterfly Effect.)
	//
	// The first time we setjmp, it returns 0.
	// But if we pop back out because of a longjmp,
	// it'll be with the value passed in the longjmp.
	
	int setjmpResult = setjmp( longjmpBuffer );
	
	if ( setjmpResult == 0 )
		{
		// Trying for the first time.
		cout << "About to do the nullptr deref." << endl;
		int *p = nullptr;
		*p = 5;
		cout << "Never reached." << endl;
		}
	else
		cout << "Failed, error = " << setjmpResult << endl;
	}
```

``` sh
181 C% g++ LinuxSignalHandler.cpp -o
LinuxSignalHandler
182 C% LinuxSignalHandler.exe
About to do the nullptr deref.
Signal 11 encountered.
Failed, error = 11
183 C%
```