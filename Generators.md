# Generators
#python 

## Iterators
- Any object that supports `iter()` is said to be iterable.
- User defined objects can be made to support iteration by implementing `__iter__()` and `__next()__` methods
  
  Sample implementation:
```python
class countdown(object):
	def __init__(self, start):
		 self.start = start
	def __iter__(self):
		return countdown_iter(self.start)

class countdown_iter(object):
	def __init__(self, count):
		self.count = count
	def __next__(self):
		if self.count <= 0:
			raise StopIteration

		r = self.count
		self.count -= 1
		return r
```

## Generators
```python
def countdown(n):
	while n > 0:
		yield n
		n -= 1
```
- Generators produce a sequence of results instead of a single value.
- Calling a generator function creates a generator object but does not execute the function.
- The generator function only executes on calling the generator object's method `__next()__`
- `yield` produces a value and suspends the function. Flow is resumed on the next call to `__next__()`
- When `yield` is exhausted, the generator function returns and raises an error (`StopIteration`).

### Generator vs. Iterator
Compared to iterators, generator objects can only be used for iteration once. After object exhaustion, the function needs to be called again to iterate once more.

### Generator Expressions
Generators can be created using comprehensions with surrounding `()` brackets.

### Advantages
- Generators can be used to produce data processing pipelines. At each step of the pipeline, an operation is defined that can be applied to the entire input stream of data. The pipelines setup using generators can be extended over the network, and across threads.
- *Declarative* Style: Instead of a data-point by data-point approach, the problem can be broken down into large operations.
- Less code. Easy to read. Faster, since only a generator object is created.

## References
http://www.dabeaz.com/generators/Generators.pdf