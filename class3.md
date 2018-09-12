Class 3
=======

*An* ***object*** *is a chunk of memory that holds some* ***value*** *from the possible set of values for the object's* ***type***

*A* ***variable*** *is a name that refers to an object*

``` cpp
int x = 3;
```

* **value** is `3`
* **type** is `int`
* **variable** is `x`

## Memory

* Objects are stored in ***memory***, which is a bunch of storage locations with ***addresses*** from 0 to a very large number

## Binary

* Base 2

* Adding zeroes to the left doesn't change the value

### Example

In decimal: 1492

In binary: 1011

## Hex

* Groups of 4 bits called *nibbles*, starting *at the least significant bit (LSB)*

* Each 4-bit group as a value from 0 to 15

* Values 10 to 15 written as A to F

* Base 16

### Example

`0111 0100 1001 1111`

749F

## Conversion

### Decimal to binary

1. Divide by 2 until the result is 0
2. The remainder is the next bit, starting with the LSB
3. Last bit is the MSB

#### Example

| Value | Result | Remainder |
|-------|--------|-----------|
| 957	 |	478  	|	1    	  |
| 478   | 239    | 0 		  |
| ...
| 1		 | 0		|	1		  |

957 decimal = 11 1011 1101 binary = 3BD hex

### Decimal to hex

1. Convert decimal to binary
2. Convert binary to hex

## More memory

### Address example

``` cpp
int x = 3;
```

The ***address*** is 0x804240c0 (2151825600 in decimal)

0x tells the computer the number is in hex

The ***variable*** is `x`

The ***value*** is 3

Changing the value of `x` would only change the ***value***, not the ***address***

* C++ uses ***value semantics***, which means initialization and assignment involve ***copying*** the ***value*** from one object to another

### Initialization

*Giving an object an inital value when it is created*

``` cpp
int x = 3;
```

### Assignment

*Overwrite old value of an object with new value*

``` cpp
x = 3;
```

The value of the above assignment is 3, so the following expression is valid.

``` cpp
a = x = 3;
```

* To get the address of a variable, use the ***address of*** operator `&`

### Address of example

``` cpp
cout << &x; //0x804240c0
```

* Don't confuse the ***address of*** operator with a ***reference type***

### Reference-to-int examples

``` cpp
int &r = x;
```

`r` is a variable whose type is ***reference-to-int***

The variable `r` is a new name for `x`

``` cpp
int swap (int &a, int &b);
```

`a` and `b` are variables whose type is ***reference-to-int***

* address of operator returns a pointer type

``` cpp
int x = 7;
cout << &x; //0x804240c0
int *p = &x;
```