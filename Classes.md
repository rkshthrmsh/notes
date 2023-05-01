# Classes
#python 

## [[Namespaces and Scopes]]

## A First Look at Classes
### Class Definition Syntax
- Simplest form of class definition:
```python
class ClassName:
	<statements>

```

### Class Objects
- Defining classes creates ***class objects*** with the class name in the enclosing scope.
- Class objects support two kinds of operations: ***attribute references and instantiation***.

### Instance Objects
- The instantiation operation creates an empty ***instance object***. However, a special method called `__init__()` can be defined to create objects with custom initial states. With an `__init__()` method, a class instantiation automatically invokes the `__init__()`. `__init__()` may take arguments, in which case the arguments are passed into the class at instantiation.
```python
class Complex:
    def __init__(self, realpart, imagpart):
        self.r = realpart
        self.i = imagpart

	def f(self):
		return 'hello world'

x = Complex(3.0, -4.5)
x.r, x.i
(3.0, -4.5)

# data attribute
x.counter = 1

# method object
xf = x.f # Here x.f is a method object, and so, even xf

# method call
xf() # This is a method call equivalent to x.f()

```
- Instance objects can only understand one operation: ***attribute references***.
- These attributes can have two kinds of valid names: ***data attributes and methods***.
- Data attributes need not be declared in the class definition. Instead, they spring into existence when they are first assigned to.
- A method is a function that belongs to an object. All attributes of a class that are function objects define corresponding methods of its instances. So, `x.f` is a valid method reference, since `Complex.f` is a function. However, `x.f` is not the same as `Complex.f` -- it is a ***method object***, not a function object.
- The special thing about method is that the instance object is passed as the first argument of the function. `x.f()` = `Complex.f(x)`
- "When a non-data attribute of an instance is referenced, the instance’s class is searched. If the name denotes a valid class attribute that is a function object, a method object is created by packing (pointers to) the instance object and the function object just found together in an abstract object: this is the method object. When the method object is called with an argument list, a new argument list is constructed from the instance object and the argument list, and the function object is called with this new argument list."

### Class variables vs. instance variables
- Instance variables are for data unique to an instance, whereas, class variables are for attributes and methods shared by all instances of a class.
- If the same attribute appears in an instance and a class, then the attribute lookup prioritizes the instance variable.
- Clients should use data attributes with care since they can be added and manipulated without affecting the validity of the methods. A naming convention would go a long way.

- The global scope associated with a method is the module containing its definition. A class is never used as global scope.

### Inheritance
```python
class DerivedClassName(BaseClassName):
    <statements>
```

- Method reference resolution: The corresponding class attribute is searched, descending down the chain of base classes if necessary.
- Derived classes may override methods of their base classes.
- Instead of overriding entirely, derived classes' methods may want to extend the base classes's methods. This can be done by just calling `BaseClassName.methodname(self, arguments)`.

- `isinstance()` checks an instance's type.
- `issubclass()` checks the class inheritance.

#### Multiple Inheritance
```python
class DerivedClassName(Base1, Base2, Base3):
    <statements>
```
- "Search for attributes inherited from a parent class as depth-first, left-to-right, not searching twice in the same class where there is an overlap in the hierarchy".