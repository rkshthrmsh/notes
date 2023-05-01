## Namespaces and Scopes
#python 

### Namespaces
- A namespace is a mapping from names to objects. Examples of namespaces include the set of Python built-in names, global names in a module, and the local names in a function invocation.
- Namespaces are completely independent and there is no relation between names in different namespaces. This implies that two different modules may both define a function with the same name.
- References to names in modules, irrespective of their data type (function, class, etc.) are attribute references.
- Namespace lifetimes:
	- The namespace containing Python built-ins are created at interpreter start-up and are never deleted.
	- The global namespace for a module is created when the module definition is read in and usually lasts until the interpreter quits. Statements executed by the top-level invocation of the interpreter are considered as part of a module named `__main__`, so they have their own global namespace.
	- The local namespace of a function is created when a function is called, and deleted when it returns or raises an exception. Recursive functions have their own local namespaces.

### Scope
- A scope is a textual region of a Python program where a namespace is directly accessible.
- At any time during execution there are up to 4 nested scopes whose namespaces are directly accessible:
	- The innermost scope, which is searched first, contains the local names
	- The scopes of any enclosing functions, starting with the nearest enclosing scopes. These typically contain non-local, but also non-global names.
	- The next-to-last scope containing the current module's global names
	- The outermost scope (searched last) is the namespace containing built-in names.

- Scopes are determined textually: The global scope of a function defined in a module is that module's namespace. irrespective of where or by what alias the function is called.
- Rebinding names outside the innermost scope requires `nonlocal` statement.
- `global` statement indicates that particular variables living in the global scope should be rebound there. Similarly, `nonlocal`  indicates that particular variables living in the enclosing scope should be rebound there.
- Without `global` and `nonlocal` statements, assignments always go into the innermost scope.