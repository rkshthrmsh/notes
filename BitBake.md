---
tags:
  - embeddedLinux
  - yocto
---
## BitBake
BitBake is a generic task execution engine that allows shell and Python tasks to be run efficiently and in parallel while working with complex inter-task dependency constraints. Conceptually, it is similar to GNU Make but has significant differences:
- BitBake executes tasks according to the provided metadata that builds up the tasks. Metadata is stored in recipe (`.bb`) and related recipe “append” (`.bbappend`) files, configuration (`.conf`) and underlying include (`.inc`) files, and in class (`.bbclass`) files. The metadata provides BitBake with instructions on what tasks to run and the dependencies between those tasks.
- BitBake includes a fetcher library for obtaining source code from various places such as local files, source control systems, or websites.
- The instructions for each unit to be built (e.g. a piece of software) are known as “recipe” files and contain all the information about the unit (dependencies, source file locations, checksums, description and so on).
- BitBake includes a client/server abstraction and can be used from a command line or used as a service over XML-RPC and has several different user interfaces.

Prior to executing BitBake, setting the BB_NUMBER_THREADS variable in the project’s `local.conf` configuration file takes advantage of available parallel thread execution on the build. A common method to determine this value for your build host is to run the following:
```
grep processor /proc/cpuinfo
```
This command returns the number of processors, which takes into account hyper-threading. Thus, a quad-core build host with hyper-threading most likely shows eight processors, which is the value you would then assign to BB_NUMBER_THREADS. A possibly simpler solution is that some Linux distributions (e.g. Debian and Ubuntu) provide the `ncpus` command.
### Syntax and Operators
#### Basic Syntax
```
# Basic variable setting
VARIABLE = "value" # Assignment occurs immediately as parsed ('hard')

# Variable expansion
A = "aval"
B = "pre${A}post"
# Curly braces are mandatory. Expansion is deffered till left-hand side variable is actually used.

# Setting a default value
A ?= "aval"
# If A is set by now, it will retain value, else "aval". Default assignment is immediate—if multiple defaults exist, the first one is taken.

# Setting a weak default value
A ??= "x"
# Weak default is the value the variable will expand to if no other value has been assigned to it. It takes effect immediately, replacing any previously defined weak default value.

# Immediate variable expansion
T = "123"
A := "test ${T}" # "test 123" event though T's final value is "456"
T = "456"

# Appending (+=) and prepending (=+) with spaces
B = "bval"
B += "additionaldata" 
# B contains "bval additionaldata". Operators take immediate effect.

# Appending (.=) and prepending (=.) without spaces
B = "bval"
B .= "additionaldata" 
# B contains "bvaladditionaldata". Operators take immediate effect.

# Appending(:append) and prepending (:prepend) (override style syntax)
B = "bval"
B:append = " additional data"

# Removal (override style syntax)
FOO = "123 456 789 123456 123 456 123 456"
FOO:remove = "123"
FOO:remove = "456"
# Foo becomes " 789 123456"

# Variable flag syntax
Foo[a] = "abc"
Foo[b] = "123"
Foo[a] += "456" 
# Foo[a] becomes "abc 456". Common to use it for documenting variables.
# VAR[doc] = "this is a variable"

# Inline Python variable expansion
DATE = "${@time.strftime('%Y%m%d',time.gmtime())"

# Unsetting variables
unset DATE
unset FOO[b]
```
#### Exporting Variables
```
export ENV_VARIABLE
```
#### Conditional Syntax
BitBake uses the `OVERRIDES` variable to control what variables are overriden after BitBake parses recipes and configuration files.
```
# Selecting a variable
OVERRIDES = "architecture:os:machine"
TEST = "default"
TEST:os = "osspecific" # This is selected because 'os' is in OVERRIDES
TEST:nooverride = "othercondvalue"

# Appending and prepending
DEPENDS = "glibc ncurses"
OVERRIDES = "machine:local"
DEPENDS:append:machine = "libmad"
# Depends becomes "glibc ncurses libmad"

# Setting bariables for a single task
FOO:task-configure = "val 1"
FOO:task-compile = "val 2"
# FOO's value for task configure will be "val 1" and
# for compile will be "val 2"

# Key Expansion
A${B} = "X"
B = "2"
A2 = "Y"
# Finally, A2 will be "Y" because key expansion happens
# when datastore is finalized
```
#### Sharing Functionality
BitBake allows sharing metadata through include files (`.inc`) and class files (`.bbclass`). It uses the `BBPATH` variable to locate needed include and class files.
##### `inherit` and `INHERIT`
When writing a recipe or class file, `inherit` can be used to inherit the functionality of a class. This is only supported within recipe and class files.
```
inherit autotools

# Multiple inherits
inherit buildhistory rm_work

# Conditional inherit
VARIABLE = ""
VARIABLE:someoverrie = "myclass"
inherit ${VARIABLE}

# Conditional inherit using Python
inherit ${@'classname' if condition else ''}
inherit ${@functionname(arguments)}
```

On the other hand, `INHERIT` can be used to the same effect within configuration files. Only `INHERIT` is supported in configuration files.
```
INHERIT += "autotools"
INHERIT += "buildhistory rm_work"
```
##### `include` and `require`
Both `include` and `require` cause BitBake to parse whatever file is specified, and insert it at that location. It is more generic than `inherit` and is applicable to any kind of shared or encapsulated functionality, or configuration, that does not suit a `.bbclass` file. One key difference between `include` and `require` is that the latter raises a parsing error if the file to be included is not found while the former does not.
#### Functions
BitBake supports four types of functions: *Shell functions*, *BitBake-style Python functions*, *Python functions*, and *anonymous Python functions*. Regardless of the type, functions can only be defined in `.bbclass` and recipe (`.bb` or `.inc`) files.
##### Shell Functions
```
some_function() {
	echo "Hello World"
}
```
Since these functions will be executed by `/bin/sh`, which may be any shell, they should be free of bashisms. Overrides and override style operators (`:append`, `:prepend`, etc.) can be applied to them as well.
##### BitBake-Style Python Functions
These functions are written in Python and executed by BitBake or other Python functions using `bb.build.exec_func()`. Similar to shell functions overrides and override style operators can be applied to them.
```
python some_function() {
	d.setVar("TEXT" "Hello World")
	print d.getVar("TEXT")
}
```
##### Python Functions
These are functions written in Python and executed by other Python code.
```python
def get_depends(d):
	if d.getVar('SOMECONDITION'):
		return "dependencywithcond"
	else:
		return "dependency"

SOMECONDITION = "1"
DEPENDS = "${@get_depends(d)}"
```
- These functions can take parameters
- `bb` and `os` Python modules are automatically available
- BitBake datastore is not automatically available—it needs to be passed in as an argument
##### Anonymous Python Functions
These functions always run at the end of parsing regardless of where they are defined. They also run in the same order as they are defined.
```
python() {
	if d.getVar('SOMEVAR') == "value":
		d.setVar(`ANOTHERVAR`) = "value2"
}
```
#### Tasks
Tasks are BitBake's execution units that make up the steps that can be run for a given recipe. They are only supported in recipes and classes, and, by conventions start with `do_`. Only shell functions of BitBake-style Python functions can be promoted to tasks. Promotion is done by using the `addtask` command:
```
addtask <function> after <pre1> [<pre2>, ..] before <post1> [<post2>, ..]
```

A task can be deleted by using the `deltask` command. However, upon deletion, the dependencies of the task are not reconnected. Dependencies can be kept intact by using the `[noexec]` varflag.
```
<task>[noexec] = "1"
```
#### Variable Flags
Variable flags or varflags control a task's functionality and dependencies. BitBake has s defined set of varflags available for recipes and classes. The full list and their meanings can be found [here](https://docs.yoctoproject.org/bitbake/2.4/bitbake-user-manual/bitbake-user-manual-metadata.html#variable-flags).
#### Dependencies
BitBake enables efficient parallel processing by defining dependencies at the task level. Dependencies can exist both between tasks within a single recipe and between tasks in different recipes.
- Dependencies internal to the `.bb` file are managed by the `addtask` directive:
```
addtask <function> after <pre1> [<pre2>, ...] before <post1> [<post2>, ...]
```
- Build dependencies are managed by the `DEPENDS` variable. This varialble is used to store a list of all tasks that the current task depends upon. The `[deptask]` varflag for tasks signifies the task of each item listed in `DEPENDS` that must complete before this task can be executed.
```
do_configure[deptask] = "do_populate_sysroot"
```
- Runtime dependencies are managed by the `PACKAGES`, `RDEPENDS`, and `RRECOMMENDS` variables. The `PACKAGES` variable lists runtime packages. Each of those packages can have `RDEPENDS` and `RRECOMMENDS` runtime dependencies. The `[rdeptask]` flag for tasks is used to signify the task of each item runtime dependency which must have completed before that task can be executed.
```
do_package_qa[rdeptask] = "do_packagedata"
```
- Recursive dependencies are handled by the `[recrdeptask]` flag. BitBake looks through the build-time and runtime dependencies of the current recipe, looks through the task’s inter-task dependencies, and then adds dependencies for the listed task. Once BitBake has accomplished this, it recursively works through the dependencies of those tasks. Iterative passes continue until all dependencies are discovered and added.
```
do_rootfs[recrdeptask] += "do_packagedata"
```
This statement says that the `do_packagedata` task of the current recipe and all recipes reachable (by way of dependencies) from the image recipe must run before the `do_rootfs` task can run.
- BitBake uses the `[depends]` flag in a more generic form to manage inter-task dependencies. This more generic form allows for inter-dependency checks for specific tasks rather than checks for the data in `DEPENDS`.
```
do_patch[depends] = "quilt-native:do_populate_sysroot"
```
#### Task Checksums and Setscene
BitBake uses checksums (or signatures) along with the setscene to determine if a task needs to be run. These checksums are stored in `STAMP` and can be examined with:
```
bitbake-dumpsigs
```
### Troubleshooting Build Failures
BitBake provides a number of useful tools and commands to trace down problems with a build.
#### Isolating Errors
- Showing list of all recipes:
```
bitbake-layers show-recipes
```
- List all tasks within a recipe:
```
bitbake -c listtasks <recipe>
```
- Rebuild a recipe:
```
bitbake -c clean <recipe> && bitbake <recipe>
```
#### Dumping the Environment
- Dump all global environment variables:
```
bitbake -e | less
```
- Dump image recipe variables:
```
bitbake -e <image_type> | less # image_type could be core-image-minimal
```
- Dump recipe's package environment and locate its working directory:
```
bitbake -e <recipe> | less # or grep and search for "^WORKDIR="
```
#### Using `devshell`
BitBake provides a development shell so that build commands can be run manually in an interactive environment.
```
bitbake -c devshell <recipe>
```
This first extracts and patches the source code for the recipe. Then, it opens a new terminal in the recipe's source directory (with the environment set up). Once inside, commands such as `./configure`, `make`, or the cross-compiler can be invoked using `$CC` directly. 
#### Graphing Dependencies
BitBake enables tracing dependencies and navigating them.
```
bitbake -v <recipe>
bitbake -g recipe -u taskexp # Opens task explorer
```


In addition, task logs can be found in `tmp/work/<arch>/<recipe>/<version>/temp`. Moreover, more logging can be added by choosing the logging level, which is defined in `meta/classes/logging.bbclass`. All recipes that inherit `base.bbclass` automatically inherit this class.