# Modules
#python 

- Modules are files with which contain common definitions and can be included in an interactive instance of the interpreter or a script. The module name is the file name containing the Python definitions and statements.
- Each module has its own namespace. 
- Entire modules or contents of modules can be imported into the namespace of a script. The last style shown below is frowned upon because it leads to poorly readable code and introduces an unknown set of names into the interpreter.
  
```python
import numpy as np
from matplotlib import plt
from matplotlib import plt as plot
from scipy import * # frowned upon
```

- Modules can be run like scripts by using the following piece of code at the end of the module file. This is often done to provide an interface for testing the module.
```python
if __name__ == "__main__":
	import sys
	<execute module>
```

- While searching for modules, Python first searches for built-in modules with the name and then in a list of directories given by the variable `sys.path`.
- Python speeds up the loading of modules by storing a compiled version in the `__pycache__` directory under the name `module.version.pyc`. This is true unless, 
	- the module is loaded from the command line in which case the module is recompiled; or
	- if there is no source module, in which case Python does not check the cache.
- Some modules are built into the Python interpreter. Such modules, albeit not part of the core, are provided for efficiency or OS specific primitives.
- `dir()` function can be used to find the names a module defines. Ex.: `dir(builtin)` shows lists the names of built-in functions and variables.

## Packages
- Packages are collections of modules and serve as a way to structure Python modules.
- When importing a package, Python searches directories on `sys.path` for an `__init__.py` file. `__init__.py` can be an empty file or execute initialization code.
```python
import sound.effects.echo  # echo is a module in the sound package
```
- The `__path__` special attribute can be used to initialize a list containing the paths of directories containing the package's `__init__.py`. This can be used to extend the features of the package in distributed directories.