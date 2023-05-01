# Iterators and Iterables
#python 

- An iterator object is one that can be iterated over a collection of data. Examples include lists, tuples, dictionaries, and sets.
- Iterators handle two main actions:
	- Returning data from a stream or container one item at a time.
	- Keeping track of the iterator state which includes the current and visited items.
- A python object needs to implement two special methods in order to be an iterator. These methods together are called the **iterator protocol**. The methods are:
	- `__iter__()`: Which is called to initialize the iterator. The `__iter__()` method must return an iterator object. In the simplest case, this method will return `self`--the iterator itself.
	-  `__next__()`: A method to iterate over the iterator and return the next value in the data stream.  In addition, this method should raise a `StopIteration` exception when the data stream is exhausted.

```python
class iterator_example():
	def __init__(self):
		pass

	# Iterable Protocol: Required to make iterator_example an iterable.
	# The __next__() method is not necessary for an iterable.
	def __iter__(self):
		return self

	# Iterator Protocol: __next__() along with __iter__() defines the 
	# iterator protocol.
	def __next__(self):
	# Define next() behavior here
		pass
	
```

- Iterators can also be created easily by inheriting from the `Iterator` class from the `collections.abc` module. The `Iterator` class predefines the `__iter__()` standard method so it is easier to manage inheritance and reduce replication for other iterators. However, it is still necessary to write the `__next__()` method.
```python
from collections.abc import Iterator

class iterator_example(Iterator):
	def __init__(self):
		pass

	def __next__(self):
		pass
```

- Using iterators has many advantages. The user can:
	- On-demand generation of data.
	- Pause iterations, including the evaluation, till the next value is required.
	- Optimize memory usage.
	- Manage data streams of infinite or unknown lengths.
- On the other hand, iterators also have a few limitations:
	- An iterator can only be used once. They cannot be restarted after exhaustion.
	- One can only move forward through an iterator (defined by `__next__()`) but never backwards.
	- It is not possible to know the length of the number of items in the iterator.
	- Items in an iterator cannot be indexed.
- However, these limitations can be overcome by writing custom `__next__()` methods with an `except StopIteration` to handle the iteration exhaustion. 

- The builtin `next()` function takes a second argument called `default` which determines the return value of the function when `StopIteration` is encountered.

- As mentioned in the first code example, the iterable protocol, i.e., the implementation of `__iter__()` method makes a Python object an iterable. Another technique to make an object iterable is by implementing the *sequence protocol* which includes the following methods:
	- `__getitem__(index)` which returns an item from the iterable at the integer `index`.
	- `__len__()` which returns the length of the sequence.
 When these methods are implemented, Python automatically treats the object as an iterable.

## Comparison between Iterators and Iterables
| Feature                             | Iterators | Iterables |
| ----------------------------------- | --------- | --------- |
| Can be used in `for` loops directly | Yes       | No        |
| Can be iterated over multiple times | No        | Yes       |
| Support the `iter()` function       | Yes       | Yes       |
| Support the `next()` function       | Yes       | No        |
| Keep state information              | Yes       | No        |
| Optimize memory usage               | Yes       | No        |

## References
[1] https://realpython.com/python-iterators-iterables/