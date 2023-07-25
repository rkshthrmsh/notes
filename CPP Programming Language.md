# C++ Programming Language
#c 

- C++ was invented by Bjarne Stroustrup at Bell Labs in 1985.
- It has many advantages compared to C:
	- [[C Programming Language]] is essentially a subset of C++.
	- C++ compilers are often combined with C compilers.
	- C++ has many improvements that make programming easier. It provides a higher level of abstraction compared to C.
		- One example of this is easier basic I/O:
```c++
cout << "C++ is an improved C";
cin >> miles; // no need for formats (%d) or &
```

## Typical C++ Program
```c++
#include <iostream> // no *.h is required like in C
using namespace::std; // C++ way to bring libraries into scope

// inline was introduced to optimize function calls, 
// as well as avoid the usage of preprocessor macros (#define), which 
// are very error prone.
// Not needed with modern optimization capable compilers.
inline double convert(int mi) { return (mi * m_to_k); }

int main(void) {
	// statements
}
```

## C++ Features that are Easy
- IO stream: `cout`, `cin`.
- `for` expressions allow local declaration: `for(int i = 0; i < N; ++i)`.
- Declarations can occur anywhere in the code. In C all declarations were supposed to appear at the head of the block.
- Inferred typing or "duck typing": `auto i = 3;`, `auto x = 3.0;`. This means that the compiler automatically infers the type of the variables. In simple types its better to specify the type of the variable. Helpful in complex type declarations.
- Standard Template Library (STL): lists, stacks, vectors, efficient algorithms.
	- Vectors: Powerful, flexible, and efficient generalization of an array.
```c++
int a[N]; // C code
vector <int> a(N); // C++ equivalent
```

## Classes
- `struct` is public, and does not have methods of its own.
- C++ achieves encapsulation by adding classes to extend struct.
```c++
class rectangle {
	public:
		rectangle(double h, double w); // constructor function
		double area { return height * width; }
	private:
		double height, width;
};

// --snip--

rectangle r(2.5, 2.0);
double ar;
ar = r.area();
```

## Functions
- Function definitions in C++ are similar to C. **One key difference is support for call-by-reference**. While pointer data types can be used to simulate call-by-reference in C, C++ natively supports call-by-reference. Differences between C and C++ are shown below:
```c
void swap(int* i, int* j) {
	int temp = *i;
	*i = *j;
	*j = temp;
}
// --snip--
swap(&i, &j); // passing addresses as arguments is necessary
```
```c++
void swap(int& i, int& j) {
	int temp = i;
	i = j;
	j = temp;
}
// --snip--
swap(i, j); // variables can be passed and addresses are implied
```
- **Another important addition to functions in C++ is support for default value for arguments**. `void sum(int a, int b=5);`

## Overloading
- C++ allows functions with the same name as long as their signatures differ. The signature of a function is defined as its identifier (name), number of parameters and their types.
- When disambiguating a function call, the compiler chooses the function whose signature matches the number of arguments and their types. This is done via the signature matching algorithm.

## Generics
- Generics in C++ refers to programming with templates. A template is C++ code that provides the compiler a framework for implementing functionality for specific types.
- The compiler uses the template to elaborate code appropriate to each set of parameters passed into a templated function.
- An example of a generic function to sum over the elements of an aggregate 'summable' type (such as an array of summable type):
```c++
template <class summable>
summable sum(const summable data[], int size, summable s=0) {
	for (int i = 0; i < size; ++i)
		s += data[i];
	return s;
}
```

## Casting
- Casting refers to the conversion of one data type into another. 
- C provided the cast operator: `(<new_type>) <varialbe>` which is deprecated in C++ due to being unsafe.
- C++ provides the following four casting operators:
	- `static_cast`: Generally considered safe.
	- `reinterpret_cast`: Highly unsafe and considered equivalent to C's casting operator.
	- `dynamic_cast`: Generally used with classes.
	- `const_cast`: For casting away const-ness. Changing the declaration type of the variable is usually the recommended operation instead of casting away the const-ness.