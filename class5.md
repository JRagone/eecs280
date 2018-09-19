Class 5
=======

## Array traversal

### Traversal by index

``` cpp
int const SIZE = 5;
int array[ SIZE ] = { 1, 2, 3, 4, 5 };

for( int i = 0; i < SIZE; ++i )
	cout << *( array + i ) <<  endl;
```

The above indexing is written using ***pointer arithmetic***

The only difference is the notation, using `*` rather than `[]`

### Traversal by pointer

``` cpp
int const SIZE = 5;
int array[ SIZE ] = { 1, 2, 3, 4, 5 };

for( int *p = array;
		p < array + SIZE; ++p )
	cout << *p << endl;
```

1. Walk a ***pointer*** across the array elements
2. To get an element, ***dereference*** the pointer

## C-style strings

Strings in basic C are an array of characters. A ***sentinel null character*** marks the end of the string.

The below are equivalent:

``` cpp
char s[ 6 ] = { 'h','e','l','l','o','\0' };

//char s[ ] = "hello";
```

`s` is an array of 6 characters, not 5, because there is a ***null character*** at the end

A `char` occupies one byte

Individual characters are encoded in ASCII

``` cpp
sizeof( char ) == 1
```

``` cpp
char s[ ] = "hello";
```

## `const` keyword

``` cpp
const T *p;			// T, the object p points to,
						// cannot be changed.
T *const p;			// The pointer p cannot be
						// changed
const T *const p;	// Neither T nor p can be changed
```

`T` is the type

Using `const` keyword, you can tell the compiler that you do not intend ot change either or both:

1. The value of the pointer
2. The value of the object to which the pointer points

## More C-strings

``` cpp
const char *p = "hello";
```

If you declare a pointer to a ***const char***, the compiler puts it somewhere special, and you just get a pointer to it. you're not allowed to change it.

``` cpp
char str[ ] = "hello";
cout << str << endl;
```

You can print out C-style strings

|	| C-style strings | C++ strings |
|---|---|---|
|Libary header| `<cstrings>` | `<string>` |
|Declaration| `char cstr[ ];` <br> `char *cstr;` | `string str;` |
|Length|`strlen( cstr );`|`str.length( );`|
|Copy value|`strcpy( cstr1, cstr2 );`|`str1 = str2;`|
|Indexing|`cout << cstr[ i ];`|`cout << str[ i ];`|
|Concatenate|`strcat( cstr1, cstr2 );`|`str1 += str2`|
|Compare|`strcmp( cstr1, cstr2 );`|`str1 == str2`|

`string` to C-style string: `char *str = str.c_str( );`

C-style string to `string`: `string str = string( cstr );`

### Comparing strings

#### C-style strings

There are no built-in string operators. You can compare pointers or what a pointer points at.

To compare a string, you can:

1. Write it yourself
2. Use a library routine, e.g., `strcmp(A,B)`, which returns negative if `A < B`, `0` if `A==B`, positive if `A > B`

## Command line arguments

``` sh
g++ -Wall -Werror -01 -pedantic test.cpp -o test
```

* `g++` is the name of the program to run
* The other "words" are ***arguments*** to the `g++` program
* The ***shell*** (CLI) starts the program and passes arguments

``` cpp
argc	// the number of arguments
argv	// an array of C-style strings

int main( int argc, char **argv ) { ... }
```

``` cpp
#include <iostream>
using namespace std;

int main( int argc, char **argv, char **envp ) {
	for ( int i = 0; envp[ i ]; i++ )
		cout << envp[ i ] << endl;
}
```

### `atoi()`

`atoi` function parses an integer value encoded in a C-style string

#### Example program

Adds up command line arguments using `argv` and `atoi`

``` cpp
#include <iostream>
#include <cstdlib>
using namespace std;

int main( int argc, char **argv ) {
	int sum = 0;
	for ( int i = 1; i < argc; i++ )
		sum += atoi( argv[ i ] );
	cout << sum << endl;
}
```

## Stream input `cin` and `fstream`

### `cin`

You can use ***input redirection*** to send the contents of a file to `cin`

``` cpp
string word;
while ( cin >> word )
	cout << "word = " << word << endl;
```

``` sh
g++ words.cpp -o words
./words < words.in
```

#### `sum` using `cin` example

``` cpp
int main( ) {
	int sum = 0;
	string word;
	cout << "Enter numbers to sum" << endl;
	while ( cin >> word && word != "done" )
		sum += stoi( word );
	cout << "sum is " << sum << endl;
}
```

### `fstream`

``` cpp
string filename = "hello.txt";
ifstream fin;
fin.open( filename );
if ( !fin.is_open() ) {
	cout << "open failed" << endl;
	exit( 1 );
}
string word;
while ( fin >> word )
	cout << "word = " << word << endl;
fin.close( );
```
