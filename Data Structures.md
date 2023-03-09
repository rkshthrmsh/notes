# Data Structures
#python 

## Sequence Types
Sequence types in Python include Lists, Tuples, range, etc. Sequences can be *packed* and *unpacked* using multiple assignment.
```Python
x, y, z = t
```
where `t` is a tuple. Number of variables and elements in the sequence type should match.

## Lists
Lists are a mutable data type that support a multitude of methods. Examples of these methods include `append()`, `extend()`,` insert()`, etc. Methods that modify the list have no return value printed because Python, by default, returns `None` for these methods. This is a design principle for all mutable data types.

### Lists as Stacks and Queues
The `append()` and `pop()` list methods enable using lists as stacks. Although lists can be used as queues, adding and removing an element to the first index is an expensive operation. It is recommended to use `collections.deque` to have fast appends and pops from both ends of the list.

### List Comprehensions
Comprehensions are a concise and readable way to make lists. Example:
```python
squares = [x**2 for x in range(10)]
combs = [(x, y) for x in [1,2,3] for y in [3,1,4] if x != y]
```

A comprehension usually contains a `for` statement followed by an expression within square brackets. This statement can be followed up with more `for` and `if` clauses to provide more conditions for the list.

List comprehensions can be nested.
```Python
transposed = [[row[i] for row in matrix] for i in range(4)]
```

## Tuples
- Tuples are immutable and usually contain a heterogenous sequence of elements. 
- Mutable objects can be used to compose tuples. 
- Tuples can be created out of mutable objects. Tuples containing no elements (null tuple) or one element require special construction styles:
```Python
empty = ()
singleton = ('Hello',)
```

## Sets
- Sets are unordered collections with no duplicate elements. 
- This data type supports set operations such as union, intersection, difference, etc. Sets can be created using  `{}`, `set()`, or comprehensions.
```Python
a = {x for x in 'abracadabra' if x not in 'abc'}
```

## Dictionaries
- Dictionaries are associative memories or associative arrays which are indexed by *keys*.
- Keys can be any immutable data type such as string, number, or even tuples (as long as the tuple does not contain mutable data).
- Keys in a dictionary must be unique.
- Similar to other data structures discussed here, dictionaries can be created by providing key-value pairs in `{}`, `dict()`, or comprehensions.

## Looping Techniques
- When looping, key-value pairs can be retrieved using the `items()` method.
- `enumerate()` provides the position index and corresponding value iterating though a sequence.
- `zip()` can be used to pair and loop over two or more sequences.
- `reversed()` method for reversing sequence order.
- `sorted()` method for sorting sequence order.
- It is recommended to make a new copy of a list for iteration instead of mutating an existing one.

## Comparing Sequence Types
- Comparison of sequence types uses lexicographical ordering: First two items are compared, and the outcome is determined. Based on this further elements are compared.
- A sequence object that is a sub-sequence of another is deemed to be smaller.