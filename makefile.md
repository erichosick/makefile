# Makefiles

# Introduction

## Make - What is it?

A primary usage of **make** is to compile and link source code in a specified order (see [Make - Other Uses](#make---other-uses)).

## Conventions

Many conventions are used in this manual. There are other ways to accomplish the same goals. Please keep this in mind and [git fork][git-fork] this project when you see ways to improve on it.

## Other Resources

For a complete manual on make check out [the make manual][make-the-manual].


## Requirements

- Mac OSX
- TODO: Test other OSes/etc.

# Lesson01

## Running Make

By default, make runs against a file named **Makefile**.

```markdown
$ make
```

Use the *-f* or *--file* flag to run make against another file.

```markdown
$ make -f makefile_01
$ make    // same as make -f Makefile
```

See [Running Make - Advanced](#running-make---advanced) for additional make options.

## Make File

Makefiles contain:

- **[Rules](#rules)** which describe how to create a file
    - Example: how to make a *.o* object file from a *.cpp* file
- **[Variables](#variables)** specify a text string value that can be substituted within a makefile
- **[Comments](#comments)** in a makefile
- **Others** - To Be Done

## Rules

We define an **explicit rule** as follows:

```markdown
target : normal-prerequisites
	recipe
	...

# WARNING: The recipe MUST be preceded by a tab character	
```

- **target** - the name of file we want to make
    - see also [Rules - Phony Rules](#rules---phony-rules)
    - **Warning:** By default, **make runs the first rule in a make file**.
- **normal-prerequisites** - Other targets required **before** this target can be made
	- note: The recipe of a target is not ran if none of the dependencies changed: a powerful feature of make
	- see see [Rules - Advanced](#rules---advanced)
- **recipe** - One or more commands to execute on the command line required to generate the target
	- **WARNING** - A recipe **MUST** start with a tab character to be treated as a recipe

A **rule** can be seen as a goal: a goal being a target make stives to update.

### Example 01 - lesson01/makefile_01

We have three files *add.h*, *add.cpp* and *main.cpp*. To build a program called **add**, we need to run *g++* to compile and build the object files from *add.cpp* and *main.cpp*.

We then need to use *g++* to link the two object files into a final executable.

All of this needs to be done in the right order.

On the command line, we would do something like this:

```markdown
$ g++ -c -std=gnu++11 add.cpp -o add.o
$ g++ -c -std=gnu++11 main.cpp -o main.o
$ g++ -std=c++11 -Wextra -Wall add.o main.o -o add
$ ./add   // run it
```

Using a make file, we want to build an executable named *add*. *add* is the executable target with two prerequisites: *add.o* and *main.o*.

```markdown
add: add.o main.o
	g++ -std=c++11 -Wextra -Wall add.o main.o -o add
```

We will need to provide **rules** on how to build *add.o* and *main.o*:

```markdown
add.o: add.cpp
	g++ -c -std=gnu++11 add.cpp -o add.o

main.o: main.cpp
	g++ -c -std=gnu++11 main.cpp -o main.o
```

The files *add.cpp* and *main.cpp* don't need to be "made" because they are created by us. However, *add.o* and *main.o* depend on *add.cpp* and *main.cpp* respectively. So, we add them to the prerequisites.

The final make file looks like this:

```markdown
add: add.o main.o
	g++ -std=c++11 -Wextra -Wall add.o main.o -o add

add.o: add.cpp
	g++ -c -std=gnu++11 add.cpp -o add.o

main.o: main.cpp
	g++ -c -std=gnu++11 main.cpp -o main.o
```

To run the final make file:

```
$ cd lesson01
$ make -f makefile_01
```

This will run the default rule add.

## Runing a Rule

To run a **rule** other than the default **rule**, provide the name of the **rule** in the command line.

```makefile
$ make -f makefile_01 main.o
```

Causes the **rule** *main.o* in *makefile_01* to run.

## Comments

Comments start with a `#`: causing the rest of the line to be ignored.

```makefile
# This makefile only has a comment
```

- To get a literal #, escape it with \\#
- also see [Comments Advanced](#comments---advanced)

## Variables

*Makefile_01* has repetition which we can remove by using variables.

Defining a **deferred variable** is done using the `=` symbol as follows:

```makefile
CC		= g++
```

Using a variable is done as follows:

```markdown
add: add.o main.o
	$(CC) -std=c++11 -Wextra -Wall add.o main.o -o add
```

Notice text `g++` in the recipe is now replaced by `$(CC)`. `CC` is a **deferred variable** so make will replace any `$(CC)` with the literal value `g++`.

A variable specifies a text string value for a variable that can be substituted into the text later.

### Example 02 Variables - lesson01/makefile_02

Let's define variables.

```markdown
CC		= g++
CFLAGS	= -Ofast -c -std=c++11 -Wextra -Wall
LDFLAGS	= -std=c++11 -Wextra -Wall
OBJ		= add.o main.o
EXE		= add

$(EXE): $(OBJ)
	$(CC) $(LDFLAGS) $(OBJ) -o $(EXE)

add.o: add.cpp
	$(CC) $(CFLAGS) add.cpp -o add.o

main.o: main.cpp
	$(CC) $(CFLAGS) main.cpp -o main.o
```

We are even able to use variables for the taget name as can be seen by the `EXE` variable.

Note: A lot of these variable names are standard conventions: for example `CC` is used to represent the compiler used.

Again notice that `$(OBJ)` is replaced by the literal text `add.o main.o`.

## Rules - Phony Rules 

A **rule** that **does not** create a file is called a phony rule. By convention, we make a phony rule `.PHONY`. Let's add in rules to remove any temporary files generated by our makefile.

#### Example 03 Phony Rules - lesson01/makefile_03

*makefile_03* is *makefile_02* with the following added:

```makefile
.PHONY : clean

clean :
	-rm -f $(OBJ) $(EXE)
```

- prefixing a rule with a `.` marks it as phony so make doesn't accidentally try to create an actual file called clean
- Placing a `-` to the beginning of the recipe ignores errors.

We can now clean any files by running the following:

```
$ make -f makefile_03 clean
```

Note: `clean` is, by convention, a rule to clean all files generated by the *Makefile*.

# Lesson02

## Directories and Project Layout

By convention, source code should be located in a directory other than root (such as *src*).

We need to let both the compiler **and** make know where our files are.

## Project Directory Structure

By convention, we will setup our project directory as follows:

- **obj**: Location of all object files generated by make
- **src**: Location of source code
- **inc**: Location of header/include files

## Make, vpath and the % Pattern Matcher

`vpath` (note lower case) is used to setup a 'search path' to locate types of files like header files, source code files, etc.

Let's setup a vpath for our header, source and object files:

```makefile
vpath %.h inc
vpath %.cpp src
vpath %.obj ${OBJ_DIR}
```

Note: If you fail to setup the vpath you may get an error like: "make: *** No rule to make target '.....'. Stop."

We use the `%` pattern matcher to tell make to match all files of a given extension. For example, the `%h` in `vpath %.h inc` says to match all files that end in *.h*.


Let's create deferred variables for all of the directories in our project:

```makefile
INC_DIR	= inc
SRC_DIR	= src
OBJ_DIR	= obj
```

Let's make sure make places the object files into the correct directory:

```makefile
OBJ		= ${OBJ_DIR}/subtract.o ${OBJ_DIR}/main.o
```

Notice the `OBJ` deferred variable also contains a deferred variable: `OBJ_DIR`.

Remember, not only do we need to let make know what directories make files are in, we also need to let the linker know where the files are.

g++ takes the -I flag and the location of included files.

```
$(CC) $(LDFLAGS) -I${INC_DIR} $(OBJ) -o $(EXE)
```

The above converts to:

```
g++ -std=c++11 -Wextra -Wall -Iinc obj/subtract.o obj/main2.o -o bin/subtract
```

The final lesson02/Makefile:

```makefile
CC		= g++
CFLAGS	= -Ofast -c -std=c++11 -Wextra -Wall
LDFLAGS	= -std=c++11 -Wextra -Wall

INC_DIR	= inc
SRC_DIR	= src
OBJ_DIR	= obj

OBJ		= ${OBJ_DIR}/subtract.o ${OBJ_DIR}/main.o
EXE		= subtract

vpath %.h ${INC_DIR}
vpath %.cpp ${SRC_DIR}
vpath %.obj ${OBJ_DIR}

$(EXE): $(OBJ)
	$(CC) $(LDFLAGS) -I${INC_DIR} $(OBJ) -o $(EXE)

${OBJ_DIR}/subtract.o: subtract.cpp
	$(CC) $(CFLAGS) ${SRC_DIR}/subtract.cpp -o ${OBJ_DIR}/subtract.o

${OBJ_DIR}/main.o: main.cpp
	$(CC) $(CFLAGS) ${SRC_DIR}/main.cpp -o ${OBJ_DIR}/main.o

.PHONY : clean

clean :
	-rm -f $(OBJ) $(EXE)

```

Try to do the following in the lesson02 directory:

- Using make, create the subtract executable
- Run the newly created substract executable
- Using make, clean up all the files created by make
- Using make, only build subtract.o

Answers:

```
$ make
$ ./subtract
$ make clean
$ make obj/subtract.o
```

Note: To run subtract, you must prefix it with the current directory `./`. This is because, by default and for security reasons, the current directory is not part of the search path used by the command line.

# Lesson03

## Rules and Pattern Matching

Notice in our prior make file that we had to explicity define a rule to create an object file from a cpp file.

```makefile
${OBJ_DIR}/subtract.o: subtract.cpp
	$(CC) $(CFLAGS) ${SRC_DIR}/subtract.cpp -o ${OBJ_DIR}/subtract.o

${OBJ_DIR}/main.o: main.cpp
	$(CC) $(CFLAGS) ${SRC_DIR}/main.cpp -o ${OBJ_DIR}/main.o
```

There is still a lot of repition going on here. We are in luck because we can use patern matching in our rules.


// TODO: Finish lesson 3






### Running Make - Advanced

```
$ make -k or make --keep-going
```

```
$ make -r // remove implicit rules
```

```
$ make -d // debug
```

### Rules - Advanced

```
target ... : normal-prerequisites | ordered-only-prerequisites... \
	recipe
   ...
```

- see .DELETE_ON_ERROR in target




## Conventions

### Variable Names

TODO

## Implicit Rules

Implicit Rules (cc -c main.c -o main.o is already there)


### Comments - Advanced

TODO: Simplify these rules for comments

- You cannot use comments within variable references or function calls: any instance of # will be treated literally (rather than as the start of a comment) inside a variable reference or function call.
- Comments within a recipe are passed to the shell, just as with any other recipe text. The shell decides how to interpret it: whether or not this is a comment is up to the shell.
- Within a define directive, comments are not ignored during the definition of the variable, but rather kept intact in the value of the variable. When the variable is expanded they will either be treated as make comments or as recipe text, depending on the context in which the variable is evaluated.


### Splitting Long Lines

Use the \ to split long lines

```markdown
add: add.o \
     main.o
	g++ -std=c++11 -Wextra -Wall add.o main.o -o add
```

Notice main.o is no longer on the same line as add.o but it is still a prerequisites.


## Make - Other Uses

// TODO

## TODO

- 3.3 Including Other Makefiles

[make-the-manual]: https://www.gnu.org/software/make/manual/make.html
[git-fork]: https://github.com/mechanisms/makefile#fork-destination-box
[makefile-and-directories]: https://stackoverflow.com/questions/1814270/gcc-g-option-to-place-all-object-files-into-separate-directory


