# Instance Method, Class Method, and Static Methods
#python 

```python
class MyClass():
	# Instance method
	def method(self):
		pass

	# Class method
	@classmethod
	def class_method(cls):
		pass

	# Static method
	@staticmethod
	def static_method():
		pass
```

## Instance Method
- The regular methods in a Python class are called *instance methods*. Instance methods are applicable to the instance object of a class they are defined in. Through the `self` parameter, they have access to the attributes of the class.
- Instance methods can also access the class itself through the `self.__class__` attribute. Therefore instance methods have the ability modify class state as well.

## Class Method
- The *class method* marked by the `@classmethod` decorator accepts a `cls` parameter instead of a `self` parameter. Hence, the class method has access to the class attributes / state but not to any of the instance attributes.

## Static Method
- *Static methods* marked by the `@staticmethod` decorator have access neither to the class (`cls`) nor the instance (`self`) attributes.
- Static methods are a way to namespace certain methods to class or class instance scopes.

## References
[1] https://realpython.com/instance-class-and-static-methods-demystified/