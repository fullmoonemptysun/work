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
```
targets: prerequisites 
	command 
	command 
	command
```
- targets are files names, separated by spaces (normally only one per rule)
- commands are a series of steps used to make the target(s). Need to start with a **TAB**
- prerequisites are also file names, need to exist before the commands for the target can be run. Also called dependencies.

## Essence of Make

```hello:

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
```
blah:
	gcc blah.c -o blah
```
This will create a blah file if it doesn't exist by executing that command. But when we try to rerun make after a change it will say "make: 'blah' is up to date" and not recompile it.

To solve this, we do:
```
blah:blah.c
	gcc blah.c -o blah
```
Now make will only run if blah does not exist OR ***blah.c is newer than blah***

- The last step is essence of make.
- uses the fs timestamps to make this decision.


```
some_file: other_file echo 
	"This will always run, and runs second" 
	touch some_file 
other_file: 
	echo "This will always run, and runs first"
```
Both run because other_file is never created
## Make Clean 
Used to remove a file/target created
```
some_file:
	touch some_file

clean:
	rm -f some_file
```

clean will never run until specified as a target in cmdline. (only the first ever target runs if no dependencies)

## Variables
- use := for assignment
- $(var) or ${} for expansion

```
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
