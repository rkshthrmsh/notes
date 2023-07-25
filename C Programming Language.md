# C Programming Language
#c

This is a primer on the C programming language derived from https://www.coursera.org/learn/c-for-everyone/

## 
- C is a Systems Implementation Language. It is near the bare-metal and requires a good understanding of how the processor deals with memory.
- It was invented by Dennis Ritchie, at Bell Labs, to write the UNIX operating system along with Ken Thompson.

## Compiling, Debugging, and Running a Program
- C compilers: gnu with the `gcc` on NIX systems. Visual Studio for Windows systems.
- Compiling:
```
gcc -o <output_executable> <input_code>.c
```
- Debugging: `gdb` can be used to debug a C program. To enable debugging, the input code must be compiled with the `-g` flag:
```
gcc -g -o <output_executable> <input_code>.c
```

## Typical C Code
```c
#include <stdio.h> // Preprocessor directive to inlude libraries

#define PI 3.14 // Preprocessor directive for defining macros

int main() { // Point of program entry
   // statements
   return 0; // Nominally 0 is returned upon successful execution
}
```

## Character Sets and Tokens
- A program in C is made up of characters, which are called tokens.
- These tokens include: comment indicators, preprocessor directives, identifiers, operators, punctuation.
- Some identifier names are special or reserved and are known as keywords. C has a set of ~30 keywords. These keywords cannot be used as ordinary identifiers.

## Fundamental Types
- C has three fundamental types: `char`, `int`, and `double`.
- The `int` type has modifiers: `unsigned`, `short`, `long`.
- The `double` type has modifiers: `float`, `double`, and `long double`

## Control Flow
- C provides the `if` and `else` construct to control the flow of a program.
- A `switch` statement with `case` arms is a more general form of the if-else. Each `case` arm is expected to have a `break` to avoid case arm fall-through execution.
- `while` is a simple loop for iteration. `while` loops can use `break` and `continue` to control re-entry and exit from the loop.
- `for` provides iteration in a concise, controllable manner.
- C also provides a conditional operator (ternary operator): `?:`

## Functions
### Function Definition
```
<output_type> <function_name> (<parameter_list>) {
  <statements>
}
```
- Function prototypes or signatures allow the usage of a function in `main` without first defining it. Placing signatures before `main` allow the user to make an outline of the function before diving into the details.

- **Arguments to functions in C are, by default, call-by-value**. What this means is that the functions behave as though they make a local copy of the arguments before the execution of the function body. As a consequence, the argument in the outer scope is left unaffected after a function call.

- **Functions implicitly convert both arguments and returns to ensure they match the function definition**.

## Storage Class
- `extern` is a storage class that ensures that the lifetime of the variable is the lifetime of the program. 
- A `static` variable within a function implies that the value is going to be retained between successive calls of the function.
- `const` is NOT a storage class. It simply indicates that the value should not be changed.

## Pointers and Simple Arrays
### Pointers
- Pointers are derived types that are used to store the address of another variable.
- Pointer types are usually declared using an `*` after the data type. For example, `int* p` is a pointer to an integer data type.
- Pointers can be dereferenced using the `*` operator.
```c
double* ptr_to_sum = &sum; // pointer to double variable 'sum'
```
- **Pointers can be used to simulate call-by-reference in C**. An example of this is:
```c
int func(int* a) {
	// statements
}

int main() {
	int a = 4, b;
	b = func(&a); // example of calling by reference
}
```

### Arrays
- Arrays are aggregate data types which contain multiple elements of a single data type. The following are examples of initializing arrays in C:
```c
int data[5];
int data[5] = {1, 2, 3, 4, 5};
int data[5] = {0}; // five-element array initialized to all 0s
int data[] = {1, 2, 3, 4, 5}; // Compiler infers size of array as 5
```
- **Strings in C are char arrays**.

#### Arrays as Parameters
- Arrays are typically passed as parameters through call-by-reference. When this is done it is necessary to pass the size of the array as a separate parameter since there is no way for the callee function to understand what the size of the array might be.
```c
// here array[] in the argument list is equivalent to an array pointer
void print_array(int size, int array[]) { 
	// statements
}
```

## Abstract Data Types
- Abstract Data Types or ADTs are user-defined data types that separate the interface of a data type from its implementation. They encompass both the data itself as well as functionality on the data.
- Since C is a weakly typed language with lenient implicit type conversion, defining abstract data types enables the compiler to conduct type-checking.

### Enums
- Enums enable the definition of user-defined data types for modelling specific use-cases. 
- With an enum data type, a variable can take only one of the variant values within the enum.
```c
enum day {sun, mon, tue, wed, thur, fri, sat}; // declare enum type
enum day today = fri;
```

### Structs
- Structs are another class of user-defined types that can contain multiple heterogenous members of different data types.
- Struct declarations can be made more readable by using `typedef` to provide an alias.
- Struct members can be accessed in two ways:
	- Using `.` after the struct variable name.
	- Using pointers with the `->` operator.
```c
struct card { // definition
	int value;
	char suite;
};

struct card {
	int value;
	int suite;
} deck[52]; // variable declaration

// Using typedef
typedef struct {
	int value;
	int suite;
} card;

card deck[52];

// Accessing struct members
deck[0].value = 1; // Set first card to ace 'A'
struct card* ptr_to_first_card = &deck[0];
ptr_to_first_card->suite = 's'; // Set first card to spades 's'
```


## The Preprocessor
- Preprocessor directives are typically used to link standard libraries, and define macros. 
- The preprocessor directives, usually lines starting with `#` are processed before compiling the program.
- Macros can be used to define almost anything, as long as the macro can be expanded to valid tokens. Examples include, providing aliases for operators and functions as syntactic sugar,  and preprocessor style "functions" `#define SQ(x) ((x) * (x))`.

## File IO
- File IO in C requires a `FILE*` (file pointer) data type.
- `stdio` provides the necessary functions for manipulating files.
- Files need to be opened before reading or writing. This can be done using the `fopen` function.
- It is good practice to close files to release the OS resources. This is done with the `fclose` command.
```c
FILE* ifp;
ifp = fopen("<file_name>", "<access>"); // <access> can be r, w, r+, w+
fclose(ifp);
```

## Command Line Arguments
- The `main` function can take two arguments to capture command line arguments given to the program.
- When taking the arguments, the `main` function will have the following signature. Here, `argc` counts the number of arguments (and includes the program itself as the first argument), and `argv` is the pointer for storing the arguments as strings.
```c
int main(int argc, char* argv[]);
```