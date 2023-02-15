## `match` Statements
#python 

- The newly introduced `match` statement in Python provides a powerful technique for data filtering. 
- Although similar in syntax to a switch-case statement, the `match...case` statement provides additional flexible pattern matching and variable binding capabilities.
```python
match <subject>:
	case <pattern(variables)>:
		<expression>
```
- The `match` statement evaluates the `subject` and checks it against the `pattern`. A pattern can do two things:
	- Verify the `subject` has a certain structure.
	- Bind variables in `pattern` to elements in the `subject`.
- Only the matching `case` statement will be executed.
- The features available with `match` are captured as an example below:
```python
command = input("What will you do next?")
match command.split():
	case ["quit"]: # single pattern
	# literals (like "quit") can be strings, numbers,
	# True, False, and None
	case ["get", obj]: # multiple patterns
		character.get(obj)
	case ["drop", *objects]: # multiple patterns
		for object in objects:
			character.drop(object)
	case ["first", (left, right), _ *rest]: # composed patterns
	# will match subjects with at least 3 elements:
	# 1st = "first", 2nd = sequence of two elements, 3rd = rest
	case ["north"] | ["go", "north"]: # Or patterns
		...
	case ["go", ("north" | "south")]: # sub-pattern matching
		...
	case ["go", ("north" | "south") as direction]: # sub-pattern
	# matching with binding
	case ["go", direction] if direction in room.exits: # patterns
	# with conditions. Here the condition is called a guard. It is 
	# a part of the case statement not the pattern.
	case ["get", Items.AXE]: # match against enums or constants
	# enum and constant names must be qualified (i.e., name with dot)
	case _: # wildcard: always match, but don't bind
		...
```

- Beyond matching sequences as shown in the example above, `match` can be used on mappings as well:
```python
match action:
	case {"text": m, "color": c} # match keys and bind to variables
		...
	case {"text": str(m), "color": str(c)}: # match keys, ensure type, 
	# and bind to variables
```