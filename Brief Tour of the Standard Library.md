# Brief Tour of the Standard Library
#python 

Python provides a set of rich builtins in the standard library for various programming purposes. Some examples include:
- `os` and `shutil` for interacting with the OS and daily file and directory management tasks, respectively.
- The `glob` module provides a function for making file lists from directory wildcard matches.
- `sys` and `argparse` provide mechanisms to process command line arguments.
- `sys` also provides `stderr`, `stdin`, and `stdout` attributes for redirecting the outputs of programs.
- `re` provides regular expression tools for advanced string processing.
- Mathematics: `math` for underlying C library functions which implement floating point math, `random` for making random selections, `statistics` for basic statistical properties.
- Internet Access: `urllib.request` and `smtplib`.
- Dates and Times: `datetime` has support for calendar arithmetic.
- Data Compression: `zlib`, `gzip`, `bz2`, `lzma`, `zipfile`, and `tarfile`
- **Performance measurement**: `timeit` provides a tool for immediately assessing performance duration. `profile` and `pstats` are additional modules that provide tools for identifying time critical sections in large blocks of code.
- **Quality Assurance**:
	- `doctest` provides a tool for scanning the docstring of a module for embedded test examples and validating them. 🤯
	  ```python
	def average(values):
    """Computes the arithmetic mean of a list of numbers.

    >>> print(average([20, 30, 70]))
    40.0
    """
	    return sum(values) / len(values)

	import doctest
	doctest.testmod()   # automatically validate the embedded tests
```
	- `unittest` is another module that allows a more comprehensive testing of python "units".
 
- Output Formatting: 
	- `reprlib` provides formatting for abbreviated displays of large deeply nested containers.
	- `pprint` for pretty printing.
	- `textwrap` for formatting paragraphs of text to fit a given screen width.
	- `locale` for culture specific data formats.
- Templating: String templating can be achieved by using the `Template` class from the `string` module.