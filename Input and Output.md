# Input and Output
#python 

Python provides several ways to format outputs when more control is necessary.

## Fancier Output Formatting
### Formatted String Literals
Formatted string literals can be used to include the value of expressions inside a string. Formatted strings are prefixed with the letter `f` or `F`. Optionally, a format specifier can be included after the expression.
```python
f'{expression:.3f}.' # three places after decimal
f'{expression:3d}.' # three decimal places
f'{expression:3x}.' # three hex places
f'{expression:3o}.' # three octal places
f'{expression:3e}.' # scietific notation
f'{expression:3}.' # three characters in total
f'{expression:>3}.' # three characters right aligned
f'{expression!a}.' # applies ascii()
f'{expression!r}.' # applies repr()
f'{expression!s}.' # applies str()
animal = 'cat'
f'{animal = }.' # self-documenting expression (=)
```

### String `format()` Method
```python
'{} blah blah {}'.format(<expr0>, <expr1>)
'{1} blah blah {0}'.format(<expr0>, <expr1>) # ref with numbers
'{expr0} blah blah {expr1}'.format(expr0=<expr0>, expr1=<expr1>) # ref with keys
dictionary = {'key0': lit0, 'key1': lit1}
'{0['key1']} blah blah {0['key2']}'.format(dictionary)
```
- The format specifiers used in f-strings can also be used with the `format()` method.

### Manual String Formatting
```python
str.rjust() # Right justified
str.ljust() # Left justified
str.center() # Center-aligned
str.zfill() # pads a numeric string with 0s
```

## Reading and Writing Files
- Opening a file:
```python
f = open('filename', '<mode>', encoding="utf-8")
# mode can be 'r'ead, 'w'rite, 'a'ppend, or 'r+' for read and write
# appending 'b' to mode opens it in binary mode. Ex.: 'rb'

with open('filename', '<mode>') as f:
	<expressions>
```
- `with` keyword ensures that the file is closed properly after its suite finishes, even in cases where an exception is raised.
- Closing a file: `f.close()`.
- Reading a file: `f.read(size)`. Returns bytes if mode is binary, or characters otherwise. 
	- Returns the entire file if `size` is omitted.
	- Empty string `''` is returned if at the end of a file.
- Reading a file line-by-line: `f.readline()`.
- Reading all lines of a file into a list: `list(f)` or `f.readlines()`.
- Writing to a file: `f.write(string)`.
	- Returns the number of characters or bytes (binary mode) written.
- Get current position: `f.tell()`
- Change file object's position: `f.seek(offset, whence)`
	- `whence`: 0 (from beginning of file), 1 (use current position), 2 (use end of file)

- The built-in `json` module can be used to structure complex, heterogenous file data.
	- `json.dump(<data>, <file_handle>)` serializes the data object to a text file.
	- `json.load(f)` deserializes the data.