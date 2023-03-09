# [[Python]] - Control Flow Tools
#python 

## `if` Statements
```python
if (<condition>):
	<expression>
elif (<condition>):
	<expression>
else:
	<expression>
```
- `elif` is short for `else if` and avoids excessive indentation compared to other languages.
- An `else...elif...elif` structure is a substitute for a `switch...case` statement found in other languages

## `for` and `while` Statements
- Python's `for` statement iterates over the items of a sequence. In comparison, languages such as C allow the user to define the start and end values for the iterations.
- However, if iterating over numbers is necessary, the `range()` function can be used. 
	- `range()` is an `iterable`, i.e., an object capable of returning its members one at a time. Examples of iterables include all sequence types (`list`, `str`, `tuple`) and non-sequence types such as `dict`, file objects, etc. In fact, any object of any class that defines an `__iter__()` method or a `__getitem__()` method that implements sequence semantics is an iterable.
- The `break` and `continue` statements break out of the innermost loop and continue to the next iteration, respectively.
- In Python, both `for` and `while` loops support an `else` clause. In `for` it is executed at the exhaustion of the loop iterable and in `while`, when the loop condition is false. However, the execution of a `break` statement skips the execution of the `else` clause.
```python
for <sequence>:
	<expression>
	break
else:
	<expression>
```

## `pass` Statements
- `pass` does nothing. It is used to create minimal-classes or as a place-holder for a function or conditional body during the early stages of coding while a developer may be thinking about higher-level aspects.

## [[match Statements]]

## [[Functions]]