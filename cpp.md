# C++ Cheatsheet by Lilian Gallon

*[@N3ROO](https://github.com/N3ROO) on github*

- [C++ Cheatsheet by Lilian Gallon](#c-cheatsheet-by-lilian-gallon)
	- [1. Introduction](#1-introduction)
		- [1.1 Things to know](#11-things-to-know)
		- [1.2 Common mistakes](#12-common-mistakes)
	- [2. Linking and compilation](#2-linking-and-compilation)
		- [2.1 Linker and compiler](#21-linker-and-compiler)
		- [2.2 Pre-processed statements](#22-pre-processed-statements)
	- [2.3 Declarations & definitions](#23-declarations--definitions)
	- [3. Debugging](#3-debugging)
	- [4. Basic pointers, references and smart pointers](#4-basic-pointers-references-and-smart-pointers)
		- [4.1 Basic pointers & References](#41-basic-pointers--references)
	- [4.2 Smart pointers](#42-smart-pointers)
	- [4.3 Buffers](#43-buffers)
	- [5. Oriented object programming (OOP)](#5-oriented-object-programming-oop)
		- [5.1 Vocabulary](#51-vocabulary)
		- [5.2 Visibility](#52-visibility)
		- [5.x Friend](#5x-friend)
		- [5.x Constructors](#5x-constructors)
		- [5.x Polymorphism](#5x-polymorphism)
	- [6. Instantiating objects](#6-instantiating-objects)
		- [6.1 Initializer lists](#61-initializer-lists)
		- [6.2 How to properly instantiate objects](#62-how-to-properly-instantiate-objects)
		- [6.3 new Keyword](#63-new-keyword)
		- [6.4 Implicit conversion and explicit keyword](#64-implicit-conversion-and-explicit-keyword)
		- [6.5 Object lifetime](#65-object-lifetime)
		- [6.6 Copy constructor](#66-copy-constructor)
	- [7. Types](#7-types)
		- [7.1 Arrays](#71-arrays)
		- [7.2 Strings](#72-strings)
			- [7.2.1 char*](#721-char)
			- [7.2.2 std::string](#722-stdstring)
			- [7.2.3 string literals](#723-string-literals)
		- [7.3 Enums](#73-enums)
	- [8. Keywords](#8-keywords)
		- [8.1 Const](#81-const)
		- [8.2 Mutable](#82-mutable)
		- [8.3 New](#83-new)
		- [8.4 This](#84-this)
		- [8.5 Extern](#85-extern)
		- [8.6 Static](#86-static)
	- [9 Operators](#9-operators)
		- [9.1 Arrow operator](#91-arrow-operator)
	- [10. Templates](#10-templates)
	- [11. Operator overloading](#11-operator-overloading)
	- [N. References](#n-references)

## 1. Introduction

### 1.1 Things to know

*or that you should not forget*

- `0 == false`
- **singleton**: class that will only be instantiated once
- **ternary operator**: `(bool expr) ? (value if true) : (value if false)`
- **structs** are the same thing as **classes,** but everything is public by default whereas everything is private by default for classes.

### 1.2 Common mistakes

- `if(a = x)` instead of `if(a == x)`
- `int* a, b;` instead of `int *a, *b;`

## 2. Linking and compilation

### 2.1 Linker and compiler

*todo, also speak about the extern keyword (or maybe should it be in the 2.3 part?). also speak about the translation unit. notes: extern: can works w/ global vars as well to prevent linking issues (duplicated var name)*

### 2.2 Pre-processed statements

They start with `#` and are evaluated before the compilation. There are several preprocessor directives, let’s see what they do:

- `#define NAME VALUE`: it will replace all the occurences of `NAME` by `VALUE`

```cpp
#define INTEGER int
INTEGER a = 0;
// "INTEGER" will be replaced by "int" before compilation
```

- `#include`: It copies and pastes the content of the included file to the current file. That’s as simple as that. For example, you can include a closing curly bracket, and it will work:

```cpp
// start file1.cpp
}
// end file1.cpp

// start file2.cpp
int Main(){
    return 0;
#include <file1.cpp>
// end file 2.cpp
```

The difference between `#include <...>` and `#include "..."` is that when you use `<>` it will search for the file in the include path while `""` will look for the file in the include path as well as relatively to the current file.

> #include <*.h> // c standard lib

- `#pragma once:` It means “only include this file once”. It prevents having duplicate declarations. Here is what you probably already saw. It has the exact same effect:

```cpp
// file.h
#ifndef _FILE_H
#define _FILE_H
// code ...
#endif
```

What it does is that when the code is copied for the first time, `#ifndef _FILE_H` is evaluated as `true` because `_FILE_H` has not been defined yet. Then, `#define _FILE_H` defines it. So when the code is copied a second time in the same file, `#ifndef _FILE_H` will be evaluated as `false`, and the code won’t be copied.

- `#if`, `#elif`, `#else` and `#endif`: Those are the conditional pre-processed statements. As long as you know how to use if, else if, and else you should be good.
- `##`: Used to concat two tokens: `x##y`

## 2.3 Declarations & definitions

**Declaration**: It tells the compiler that the declared variable / function (whatever) exists. It is a sort of promise that you make to the compiler. So if the declaration does not refer to anything, then there will be an issue during runtime saying unable to resolve symbol which is a linker error.

```cpp
// We said that GetAbsoluteFilePath exists, but we do not define what it does
// -> This is a declaration.
std::string GetAbsoluteFilePath(std::string filename);
```

**Definition**: It basically defines what the declaration declared.

```cpp
// We define what GetAbsoluteFilePath does
// -> This is a definition.
std::string GetAbsoluteFilePath(std::string filename){
	std::string path = "";
	for (const auto& entry : std::filesystem::directory_iterator("."))
	{
    if (entry.path().filename().string() == filename)
    {            
			path = std::filesystem::absolute(entry.path()).string();            
			break;
    }
	}
	return path;
}
```

## 3. Debugging

> todo how to debug

## 4. Basic pointers, references and smart pointers

> TODO: some info about const ref arguments

### 4.1 Basic pointers & References

The addresses are `8 bytes` on `x64` architectures, and `4 bytes` on `x32` architectures (also knwon as `x86`). An address is an hexadecimal number. To represent an hexadecimal number, we use the `0x` prefix.

You need to know that by heart: - `type* ptr` defines `ptr` as being an address pointing to a variable of the given `type` - `ptr` reads what is at the address pointed by ptr (it’s called dereferencing) - `&var` gives the memory address of var

Let’s see all of that in action

```cpp
// This instruction creates a pointer called ptr.
// ptr is an hexadecimal number representing an address (ex: 0x1234 for x32).
// As you can see, ptr is an address, it is not equal to 5. So by using int*,
// you make a promise that this address points at an integer. This will then
// be used when deferencing to read and write what's contained at this address.
int* ptr = new int(5);

// So you can do that. It will change the address to 4, which is wrong (error:
// can not convert from 'int' to 'int *')
// ptr = 4;

// If you want to update the variable, you need to dereference the pointer first
*ptr = 4;

// This will print the address where the address pointing to the integer is located
std::cout << std::hex << &ptr << std::endl;
delete ptr; // don't forget to free the memory (we will see that in depth later on)

// If you understand that completly, you know that you can do a lot of weird stuff
// by manipulating addresses and types
```

We can also use `&` to create **aliases**.

```cpp
int var = 5;
int &ref = var;
ref ++;
// var = 6
// ref = 6
```

Here is an other use of `&`:

```cpp
void funct(int var) {
    ++ var;
}
int var = 0;
funct(var);

// a is still 0
funct(&var)
// a is now 1
```

In the first call of `funct`, a **copy** of `var` will be passed. So it’s an other variable that will be updated, and not `var` itself. However, in the second call of `funct`, `a` is not copied, it is passed as **reference**. This reference will be updated, and as effect, `var` will also get updated. It is actually the exact same thing as:

```cpp
void funct(int* var)
{
    var = (*var);
    ++ var;
}
```

It is just **syntax sugar**.

## 4.2 Smart pointers

Smart pointers free themselves automatically.

**unique_ptr**: it's a scoped pointer. When the execution goes out of the pointer's scope, it gets destroyed. It's also not possible to copy it.

```cpp
std::unique_ptr<Car> car = std::make_unique<Car>(); // exception safe
std::unique_ptr<Car> car (new Car()); // works, but not recommended
```

**shared_ptr**: there's a counter, and when it reaches 0, it gets deleted. Can obviously be copied. That counter counts the number of references.

```cpp
std::shared_ptr<Car> car = std::make_shared<Car>(); // exception safe
```

**weak_ptr**: must be used with a shared_ptr. It makes a copy of it without increasing the counter. It means that you don't take ownership of it (so it can be destroyed when you're using it). It is useful for some specific cases.

```cpp
std::weak_ptr<Car> weakCar = car; // car is a shared_ptr
```

## 4.3 Buffers

```cpp
// Asks for 8 bytes of memory (because 1 char is 1 byte)
char* buffer = new char[8];
// Fills the memory from the address pointed by buffer to this address + 8 bytes with A's
memset(buffer, 'A', 8);

// Prints "A" and not "AAAAAAAA"
std::cout << *buffer << std::endl;

// Why? Because it is a char*, and when printing it, it will read only 1 byte at the
// address pointed by buffer. Only 1 byte because a char is 1 byte!

// Here is a little trick to show the entiere buffer
for (unsigned int i = 0; i < 8; i++)
	std::cout << *(buffer+1);
std::cout << std::endl;

// You probably won't do that in the future, but rather
const char* str = "Hello";
std::cout << str << std::endl;

// Don't forget to free the buffer (allocated on the heap)
delete[] buffer;
```

## 5. Oriented object programming (OOP)

*We won't go over everything here. We will only speak about the basic concepts of OOP (for example we will speak of the behavior of the `const` keyword in the const part of this cheat sheet so that we have everything in one place).*

### 5.1 Vocabulary

- **Functions** are also called **methods**
- **Variables** of class are called **class attributes**
- An **instance** of a **class** is an **object** (and an object is an instance)

### 5.2 Visibility

- `private`: only current class and friends can access it
- `protected`: only current class and children classes can access it
- `public`: anything can access it
- nothing: default visibility is private

### 5.x Friend

> todo (should it be in part 1?)

### 5.x Constructors

> todo

### 5.x Polymorphism

From Wikipedia, *"In programming languages and type theory, polymorphism is the provision of a single interface to entities of different types or the use of a single symbol to represent multiple different types."*. We won't go in depth here, because that's not the point of this cheat sheet, but we use Polymorphism when a class inherits from a given class. It will need to override some functions (or not), and modify some functions (or not) and so on. We will see how we can do that programmatically here.

**Virtual functions** allow us to override them in subclasses (≥ C++11: you should use the keyword `override`, it's not required although it prevents bugs).

```cpp
class Car
{
public:
	virtual void Vroom() { std::cout << "mumum" << std::endl; }
	virtual void Open() { std::cout << "bip bip" << std::endl; }
};

class Mustang : public Car
{
public:
	void Vroom() override { std::cout << "RRRRRR" << std::endl; }
};

int main(char* args, int argc)
{
	Car car;
	Mustang mustang;
	Car* probablyMustang = new Mustang();

	car.Vroom(); // prints mumum
	mustang.Vroom(); // prints RRRRR
	probablyMustang->Vroom(); // prints RRRR since probablyMustang is an instance of Mustang

	// prints bip bip: it takes the first function that matches the call in the inheritance tree
	mustang.Open();

	delete probablyMustang;
	return EXIT_SUCCESS;
}
```

**Pure virtual functions** define a class as abstract. Because if these functions are not implemented in the inheriting classes, they can't be instantiated.

```cpp
// If this function is in the Car class
virtual int GetGasType() = 0; // pure virtual function
// Then, the car class can't be instantiated.
// If the Mustang class that inherits from the Car class implements that function
int GetGasType() override { return GAS::PREMIUM; }
// Then, it can be instantiated
```

> size of classes, effect of inheriting and so on

> todo: Different types of functions (pure virtual, virtual, …)

## 6. Instantiating objects

### 6.1 Initializer lists

> todo (explain why it’s better than initializing the attributes in the constructor body)

### 6.2 How to properly instantiate objects

> todo (if we use C++, it’s for the optimizations!)

### 6.3 new Keyword

> todo (explain what it does)

### 6.4 Implicit conversion and explicit keyword

> todo

### 6.5 Object lifetime

> todo

### 6.6 Copy constructor

> todo

## 7. Types

### 7.1 Arrays

> todo (“basic arrays” , dynamic arrays, where they’re good and bad at. How to chose the array to use?) - also how to PROPERLY use std::vector

**Base**:

An array is a pointer to a block of memory. Let's see an example:

```cpp
// arr is an integer pointer to a block of memory containing 8 integers
// arr is allocated on the stack (it will get destroyed at the end of the current block)
int arr[7] = { 1, 2, 3, 5, 8, 13, 21 };

// If you want to get, let's say the element at the index 2, then, you will use
int elem = arr[4];
```

In the backstage, it will do that

```cpp
int sameElem = *((int*)(arr) + 4);
```

-  `(int*) (arr)`: casts arr to a pointer to an integer, because that's what it is
- `(int*) (arr) + 4`: that's what we call "pointer arithmetics" 4 will be scaled according to the type of the pointer. So in that case, it's an int pointer so 4 will
represent `sizeof(int)*4 = 4*4 = 16`
- `*((int*)(arr) + 4)`: gets the content of that address

We specified that sameElem is an int, so it will read `sizeof(int) = 4 bytes` from `address_of_arr + 16`. You can also write by doing so:

```cpp
arr[4] = 33;

// same thing as
*((int*)(arr)+4) = 33;
```

> If you modify an array outside of its bounds, it will modify some data in the memory that does not belong to you. And, while in debug mode it will show the error, in release mod it may not show it!

There is an other way to create arrays:

```cpp
// This one is allocated on the heap
int* arr = new int[7] { 1, 2, 3, 5, 8, 13, 21 }

// So you need to delete it once that you won't need it
delete[] array;
```

Now let's see how to properly use arrays in C++.

> todo

### 7.2 Strings

#### 7.2.1 char*

```cpp
// all of these initializations are made on the stack

const char* str1 = "something";
char str2[4] = {'s', 'o', 'm', 'e'};
char str3[5] = { 's', 'o', 'm', 'e', '\0' };

std::cout << str1 << std::endl;
std::cout << str2 << std::endl;
std::cout << str3 << std::endl;
```

We use a terminaison character (\0 or 0) to know when a string ends
- str1 prints "something" properly since the terminaison char is added in the backstage
- str2 prints weird characters at the end (it tries to print the random numbers that are in the memory)
- str3 prints "some" properly since there is the terminaison character (\0 or 0)

> char use 1 byte and are encoded in ASCII

> `char*` (without the const keyword before) is depreciated since C++11 because strings intialized that way should not
> be modified. Otherwise it would mean to dynamically re-allocate memory. There are better ways to do that.

#### 7.2.2 std::string

`std::string` is basically an array of `char` with some helpers (functions).

> \<iostream\> has the declaration of string, but \<string\> is needed because the \<\< with strings is not in \<iostream\> for example.

The following code won't work because `"some"` is a `const char[]`.

```cpp
std::string str1 = "some" + "thing";
```

However, you can do that:

```cpp
std::string str1 = "some";
str1 += "thing";
```

Because it's adding an `std::string` to an `std::string`, and not a `const char[]` to a `const char[]`. The `+` operator also works, so you can do that as well:

```cpp
std::string str1 = std::string("some") + "thing";
```

Here is an example of some helper functions:

```cpp
// find returns the position where the string was found
// npos is a static member constant value which evaluates at the
// maximum value for size_t (end of a string in brief)
bool contains = str1.find("thing") != std::string::npos;
```

#### 7.2.3 string literals

```cpp
#include <string>
using namespace std::string_literals; // for the "s"

const char*     str1 =   "Something"; // ASCII
const char*     str2 = u8"Something"; // UTF-8
const wchar_t*  str3 =  L"Something"; // depends
const char16_t* str4 =  u"Something"; // UTF-16
const char32_t* str5 =  U"Something"; // UTF-32

std::string    str6 =  "Some"s +  "thing";
std::wstring   str7 = L"Some"s + L"thing";
std::u16string str8 = u"Some"s + u"thing";
std::u32string str9 = U"Some"s + U"thing";

// Ignores escape character
const char* multiline = R"(line1
line2
line3)";
```

### 7.3 Enums

It's a way to give names to values. By default, the names will be mapped to integers (0, 1, ...). But those values can be changed.

```cpp
enum Color { RED, GREEN, BLUE };
```

Their type can also be changed.

```cpp
enum Direction { LEFT = 'l', RIGHT = 'r' };
enum Direction : char { LEFT = 'l', RIGHT = 'r' }; // since c++ 11
```

They can be used in two different ways:

```cpp
Direction dir1 = Direction::LEFT; // dir1 must be a Direction
char dir2 = Direction::LEFT; // dir2 just has to be a character
```

They are usually used to prevent using *magic number*s*.*

## 8. Keywords

### 8.1 Const

Can't modify the *content* of that pointer. The const keyword is applied on `int`:
```cpp
const int* addr1;
```

Can't modify the *address* of that pointer: The const keyword is applied on `*`:
```cpp
int* const addr2;
```

Can't modify either the *content* or the *address*. The const keywords are applied to `int` and `*`:
```cpp
const int* const addr3;
```

(Only works in classes and structs). Can't modify the class attributes in this function HOWEVER, we can update the attributes if they're marked as mutable (see part 8.2):
```cpp
int GetX() const { return x; }
```

### 8.2 Mutable

> todo: (related to const, but can be used for lambdas and so on)

### 8.3 New

The new keyword is detailed in [Instantiating objects](about:blank#63-new-keyword)

### 8.4 This

> todo

### 8.5 Extern

The `extern` keyword is used to retrieve a global function/variable that is in another translation unit.

```cpp
// other.cpp (any file that needs to use that global variable)
extern int global;

// main.cpp (needs to be only one file, you can't init a variable more than once)
#include "other.cpp"
int global = 5;
```

### 8.6 Static

It's like declaring a variable private in a class but with translation units (⇒ the variable/function will only be visible in the file where it has been declared).

> It needs to be used as much as possible if the variable/function is not global, otherwise it can be used anywhere and can lead to bad bugs (for example if there is another global variable with the same name).

You may think about the `extern` keyword to retrieve a static variable/function in another translation unit, but you can't. That's why it's "private".

```jsx
// file1.cpp
static int
```

When static is used in a scope, then the lifetime of the targeted variable/function changes.

```jsx
void Foo()
{
	static int i = 0;
	++i;
	std::cout << i << std::endl;
}

// main
Foo(); // prints 1
Foo(); // prints 2
```

As you can see, it acts as if the code was like that:

```jsx
static int i = 0;
void Foo()
{
	++i;
	std::cout << i << std::endl;
}
```

The thing is that it is not possible to modify or access `i` outside the function.

Static is also used in classes, but we saw that in part 5.4.

> TODO: speak about static in classes

## 9 Operators

### 9.1 Arrow operator

> todo

## 10. Templates

Templates are used to generate code dynamically.

```cpp
template<typename T>
void println(T value)
{
    std::cout << value << std::endl;
}
```

You can use `class` instead of `typename`, it does the exact same thing. The general convention is to use `class` when `T` is expected to always be a class, and `typename` otherwise (if other types may be expected).

Here, `println` doesn't exist. It will be created when `println` will be called according to the type given. So if you don't call `println` it won't ever be compiled. In the above example, the function will be compiled this way:
```cpp
void println(int value) { std::cout << value << std::endl; }
```

If that function already exists, then it won't generate a new one, and it will call the one that already exists.

As you probably know, you can't initialize an array with a variable length. But with templates you can, because the code will be generated dynamically.

```cpp
template <int N>
class Array {
private:
    int array[N];
public:
    int Length() { return N; }
};

// main..
Array<5> arr;
println(arr.GetSize());
```

## 11. Operator overloading

> todo

## N. References

- [http://www.cplusplus.com/](http://www.cplusplus.com/)
- [The Cherno on Youtube](https://www.youtube.com/channel/UCQ-W1KE9EYfdxhL6S4twUNw)
- [cpp reference](https://en.cppreference.com/)