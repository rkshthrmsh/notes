# Operating Systems
#os

### Make
Make is a tool that automates the build process. The build process is governed by Makefiles, which have the following general format:
```makefile
target: prerequisite1 prerequisite2 ...
	command1
	command2 ...
```
- **Target** is the name of an action or a command-generated file.
- **Prerequisites** are files required by the target.
- **Commands** are actions that the target carries out. Every command line must start with a tab character.

To run the make tool, navigate to the directory with the Makefile and type `make`. This will automatically look for the file named `Makefile` or `makefile`.

Example of a generic makefile:
```makefile
SOURCES = hello.c helper.c
OBJS = $(SOURCES:.c=.o)
TARGET = hello

CC =gcc  # compiler 
OPTS = -Wall # flags
LIBS = -lm # libraries

$(TARGET): $(OBJS)
	$(CC) -o $(TARGET) $(OBJS) $(LIBS)

%.o: %.c
	$(CC) $(OPTS) -c $< -o $@

clean:
	rm -f $(OBJS) $(TARGET)
```

### Debugging with `gdb`
C programs can be debugged using `gdb` by compiling them with the `-g` flag. Then, the compiled output can be run using the `gdb <compiled_binary>` command. This starts an interactive shell-based debugging session. In the shell use:
- `run` to run the program.
- `print <variable>` to print the value of a variable.
- `quit` to quit the debugging session.