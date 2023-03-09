## Functions
#python 

```python
# Function definition
def f(pos1, pos2=arg2, /, pos_or_kwd: int, *, kwd1: str = 'foo', **kwargs) -> str:
def f(pos1, pos2=arg2, /, *args, kwd1: str = 'foo', **kwargs) -> str:
	""" Function definitions with different parameter types and annotation.
	
	Parameters:
	- pos1: required positional argument.
	- pos2: optional positional argument (default: arg2)
	- / : demarcation for positonal and positional-or-keyword arguments.
	- pos_or_kwd: required, annotated positional or keyword argument.
	- * : demarcation for positional-or-keyword arguments and keyword arguments.
	- *args: receive variadic positional arguments and wrap them as a tuple.
	* and *args cannot be used together (* argument may only appear once)
	- kwd1: annotated optional keyword arguments.
	- **kwargs: receive variadic keyword arguments and wrap them as a dictionary.
	**kwargs must be after *args.
	- ->: return type annotation.
	"""
	...
	return bar # optional. Default value without return statement is None.

# Function call
kwa = {'kwd3': 'ipsum', 'kwd4': 'dolor'}
f(arg1, arg3, kwd1='bar', kwd2='lorem', **kwa) # **kwa unpacks the dictionary
# kwd2 through kwd4 get repacked into kwargs dictionary
```

- The first statement of a function can be a string literal. The string literal is the function's *docstring*, which usually describes the function and its parameters.
- At the time of execution, a new symbol table is used to store the values of local variable assignments in the function. 
- Variable references first check the local symbol table, then in symbol tables of enclosing functions (if any), then in the global symbol table, and finally in the table of built-in names.
- Global variables and variables of enclosing functions cannot be assigned values within a function unless specified as `global` or `nonlocal`. However, they can still be referenced.
- Arguments to the function are passed using *call by value* with the *value* being an object *reference*.
- Although functions without a `return` may appear to be like procedures from other languages, they still, in fact, return `None`.
- Default values for parameters are evaluated only once at the point of function definition in the *defining* scope. This affects how mutable objects (list, dictionary, class instance) are evaluated during multiple invocations of the same function. Since they can be mutated elsewhere in the program, the "default" may change between invocations.

### Lambda Functions
```python
lambda arg1, arg2: arg1 * arg2
```
* Lambda functions are anonymous functions created with the `lambda` keyword.
* They are restricted to a single expression.
* They are syntactic sugar for normal functions.
* Can be used in expressions that are passed to a function as an argument.