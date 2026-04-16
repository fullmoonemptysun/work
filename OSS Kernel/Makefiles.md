# Introduction
## Why?
- To recompile/compile C/C++ files in large projects
- To run certain instructions depending on what files have changed. 
![[Pasted image 20250723201146.png]]

## Alternatives?
- SCons, CMake, Bazel, Ninja. 
- For Java, there is Ant, Maven, Gradle 
- The goal of Makefiles is to compile whatever files need to be compiled, based on what files have changed. But when files in interpreted languages change, nothing needs to get recompiled. When the program runs, the most recent version of the file is used.

## Versions and types
- 3, 4
- Must use tabs to indent
```make
hello:
	echo "Hello, World"
```

## Syntax
- Set of rules
- 1 rule looks like:
```make
targets: prerequisites 
	command 
	command 
	command
```
- targets are files names, separated by spaces (normally only one per rule)
- commands are a series of steps used to make the target(s). Need to start with a **TAB**
- prerequisites are also file names, need to exist before the commands for the target can be run. Also called dependencies.

## Essence of Make

```make
hello:
	echo "Hello, World"
	echo "This line will print if the file hello does not exist"
```
- target: hello
- 2 commands
- no prereqs
- As long as the hello file does not exist, the commands will run. If it exists, they will not run.
- File names and targets are directly tied together. Typically, when a target is run (aka when the commands of a target are run), the commands will create a file (binary??) with the same name as the target. In this case, the `hello` _target_ does not create the `hello` _file_.

>[!WARNING] Side note
>Can provide cmd line args to make to select the first target. By default this is the first target in the file.

- when we do:
```make
blah:
	gcc blah.c -o blah
```
This will create a blah file if it doesn't exist by executing that command. But when we try to rerun make after a change it will say "make: 'blah' is up to date" and not recompile it.

To solve this, we do:
```make
blah:blah.c
	gcc blah.c -o blah
```
Now make will only run if blah does not exist OR ***blah.c is newer than blah***

- The last step is essence of make.
- uses the fs timestamps to make this decision.


```make
some_file: other_file echo 
	"This will always run, and runs second" 
	touch some_file 
other_file: 
	echo "This will always run, and runs first"
```
Both run because other_file is never created
## Make Clean 
Used to remove a file/target created
```make
some_file:
	touch some_file

clean:
	rm -f some_file
```

clean will never run until specified as a target in cmdline. (only the first ever target runs if no dependencies)

## Variables
- use := for assignment
- $(var) or ${} for expansion

```make
files := file1 file2
some_file: $(files)
	echo "look at this variable: " $(files)
	touch some_file

file1:
	touch file1
file2:
	touch file2

clean:
	rm -f $(files) some_file
```


---
# Targets
## The all target
- can be used ensure multiple targets run on `make` 
```make
all : one two three

one:
	touch one
two:
	touch two
three:
	touch three

clean:
	rm -f one two three
```

## Multiple targets
- if multiple targets specified, commands will run for each of them:
```make
all : f1.o f2.o

f1.o f2.o:
	echo $@

```
$@ is variable that contains the target name.


# Automatic Variables and Wildcards

## * Wildcard

- * % are wild cards
- * searches file system for matching filenames. Should be used in a wildcard function always because if used by itself, if there are no matches it is left as * only

```make
thing_wrong := *.o (don't do this)
thing_right := $(wildcard *.o) (do this)

all : one two three four

#fails because $(thing_wrong) is the string "*.o" (* not expanded)
one: $(thing_wrong)

#stays as *.o if there are no files that match this pattern.
two : *.o

#works right
three: $(thing_right)

#works right also
four: $(wildcard *.o)
```

## % Wildcard

- useful but confusing
- when used in 'matching mode' replaces one or more characters in a string. This match is called the stem.
- when used in replacing mode, it takes the stem that was matched and replaces that in a string.
#coverthismorelater


## Automatic variables
```make
hey: one two

# Outputs "hey", since this is the target name
echo $@

# Outputs all prerequisites newer than the target
echo $?

# Outputs all prerequisites
echo $^

# Outputs the first prerequisite
echo $<

touch hey

one:
touch one

two:
touch two

clean:
rm -f hey one two
```

# Fancy rules
## Implicit rules
- There are some automatic rules that Make has when compiling C code.

- Compiling a C program: n.o is made automatically from n.c with a command of the form $(CC) -c $(CPPFLAGS) $(CFLAGS) $^ -o $@
- - Compiling a C++ program: `n.o` is made automatically from `n.cc` or `n.cpp` with a command of the form `$(CXX) -c $(CPPFLAGS) $(CXXFLAGS) $^ -o $@`
- - Linking a single object file: `n` is made automatically from `n.o` by running the command `$(CC) $(LDFLAGS) $^ $(LOADLIBES) $(LDLIBS) -o $@`

The important variables used by implicit rules are:
- `CC`: Program for compiling C programs; default `cc`
- `CXX`: Program for compiling C++ programs; default `g++`
- `CFLAGS`: Extra flags to give to the C compiler
- `CXXFLAGS`: Extra flags to give to the C++ compiler
- `CPPFLAGS`: Extra flags to give to the C preprocessor
- `LDFLAGS`: Extra flags to give to compilers when they are supposed to invoke the linker

example:
```make
CC = gcc
CFLAGS = -g

blah:blah.o

blah.c:
	echo "#include <stdio.h> \n int main(){printf("hello world"); return 0;}" > blah.c

clean:
	rm -f blah*
```

## Static pattern Rules
