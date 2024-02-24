======================================================================
Topic: Learn Makefiles
Ref: https://makefiletutorial.com/


======================================================================
# Getting Started


----------------------------------------------------------------------
## Why do Makefiles exist?

When we have some changes in separate file in a large
project we really want to know which parts of that project
should be recompiled.

> Makefiles are used to help decide which parts of a large
  program need to be recompiled.

In the vast majority of cases, `C` or `C++` files are
compiled. Other languages typically have their own tools
that serve a similar purpose as `Make`.

> `Make` can also be used beyond compilation too, when you
  need series of instruction to run depending on what
  files have changed.

[EXAMPLE]
    Dependency graph that you might build with Make. If any
    file's dependencies changes, then the file will get
    recompiled:

![Dependency Graph](/inc/dependency_graph.png)


----------------------------------------------------------------------
## What alternatives are there to Make?

- `SCons`
- `Cmake`
- `Bazel`
- `Ninja`
- `Ant`, `Maven`, `Gradle` (for `Java`)

[NOTE]
    Interpreted languages like `Python`, `Ruby`, and raw
    `Javascript` don't require an analogue to `Makefiles`.
    The goal of `Makefiles` is to compile whatever files
    need to be compiled, based on what files have changed.
    But when files in interpreted languages change, nothing
    needs to get recompiled. When the program runs, the most
    recent version of the file is used.


----------------------------------------------------------------------
## Running the Examples (Hello, World!)

To allow `make` do his job we should create a `Makefile`
with special code and execute `make`.

```make
hello:
    echo "Hello, World!"
```

[WARN]
    `Makefiles` *must* be indented using TABs and not
    spaces or `make` will fail.


----------------------------------------------------------------------
## Makefile Syntax

A Makefile consists of a set of **rules**. A rule generally
looks like this:

```make
targets: prerequisites
    command1
    command2
    command3
```

- **targets** - file names, separated by spaces;
- **commands** - cmds to make target(s);
- **prerequisites** - (dependencies) file names, separated
    by spaces

----------------------------------------------------------------------
## The essence of Make

[EXAMPLE]
```make
main:
    echo "Ya delau make yo"
```

Мы помним, что таргеты это названия файлов, если у меня не
существует файла `main`, то таргет `main` будет выполнен, но
этот таргет не создает файла `main`, поэтому по окончании
его (этого правила) исполнения и повторного запуска `$ make`
мы снова получим тот же результат - вывод "Ya delau make yo".
Если же мы создадим файл `main` запустим `$ make`, то снова
получим сообщения, но на повторный запуск `$ make` make
скажет, что файл `main` уже "up do date".


>  By default cmd `make` will run the main (1st target) in
   Makefile. Plus, we can specify what target we want to
   process: `make trgX` will process target trgX.


[EXAMPLE]
```make
main_build: main.c
    gcc main.c -o main
```

> If we change file `main.c` we obviously should recompile
this file, because we have the new file `main.c` - file with
changes that a newer that the previous one. So to follow
this logic `make` uses the filesystem timestamps as an
indicator to determine if something has changed. This is
a reasonable, because file timestamps typically will only
change if the file are modified. **But it's important to
realize that this isn't always the case.** You could, for
example, modify a file, and then change the modified
timestamp of that file to something old. If you did, `Make`
would incorrectly guess that the file hadn't changed and
thus could be ignored.


[EXAMPLE]
```make
# rule 1
main: main.o
    cc main.o -o main

# rule 2
main.o: main.c
    cc -c main.c -o main.o

# rule 3
main.c:
    echo "int main() { return 0; }" > main.c
```

Запускаем `$ make` на этом примере. Поскольку мы не передали
в make правило для исполнения, то будет выполнено первое
правило. У первого правила есть зависимость от `main.o`,
поэтому перед выполнением правила 1 нужно посмотреть есть ли
где-то правило для файла `main.o`. Да, действительно второе
правило отвечает за этот файл. Так мы спустились до правила
3, у которого нет никаких зависимостей. Поэтому оно будет
выполнено, если файла `main.c` не существует или его
временная метка не совпадает с прошлой версией, иначе оно
не выполняется. После правила 3 в любом случае мы
поднимаемся к правилу 2, выполняем его команду, если его
временная метка была обновлена, и поднимаемся к правилу 1.

Команда правила 1 создает файл `main`, поэтому при повторном
запуске `$ make` make будет смотреть на его временную метку.


> timestapm(target) > timestapm(dependency)
  targets are always should be newer then it's dependency.


Если теперь сделать `$ touch main.o` и снова запустить `$
make`, то условие `timestamp(main) > timestamp(main.o)` не
выполнено, поэтому будет выполнено правило 1, но сначала мы
посмотрим на правило 2, потому что там таргет это `main.o`,
но в правиле 2 выполнено условие
`timestamp(main.o) > timestamp(main.c)` поэтому это правило
не будет выполнено, а значит, сразу выполняем правило 1.


----------------------------------------------------------------------
## Make clean

`$ make clean`, target `clean`:
1. not default target;
2. not prerequisite;
3. 2,3) => it will never run unless you explicitly call `$
   make clean`
4. There is shouldn't exists file with name `clean`

```make
main: main.c
    cc main.c -o main

clean:
    rm -f main
```

Target `clean` should remove all what have been created
after running `$ make`.

----------------------------------------------------------------------
## Variables Pt. 1

[WARNING] *Variables can only be strings*, should use
operator `:=`, but `=` is also ok.

[EXAMPLE]
```make
src_files := file1 file2

some_file: $(src_files)
    echo "Look at this variable: " $()
    touch some_file

file1:
    touch file1

file2:
    touch file2

clean:
    rm -f file1 file2 some_file
```


- `'` or `"` are equals for `Make`; They are simply
  characters that are assigned to the variable.
- Quotes are useful to `shell` or `bash`;

[EXAMPLE] the 3 cmds behave the same:
```make
a := one two     # a is set to the string "one two"
b := 'one two'   # b is set to the string "'one two'"

all:
    printf '$a'
    printf $b
```

[EXAMPLE] reference variable using either `$()` and `${}`:
```make
x := Hello World

def:
    echo $(x)
    echo ${x}

    # Bad practice, but works
    echo $x
```


----------------------------------------------------------------------
## Targets
### The 'all' target

- The `all` target use to run all other targets;

[EXAMPLE] the `all` target
```make
all: one two three

one:
    touch one
two:
    touch two
three:
    touch three

clean:
    rm -f one two three
```


### Multiple targets

- When there are multiple targets for a rule, the cmds will
  be run for each target;
- `$@` - automatic variable that contains the targets name;

[EXAMPLE] multiple targets and the `$@` variable
```make
all: f1.o f2.o

f1.o f2.o:
    echo $@
```
Equivalent to:
```make
all: f1.o f2.o

f1.o
    echo f1.o
f2.o
    echo f2.o
```


----------------------------------------------------------------------
## Automatic Variables and Wildcards

- Wildcards (подстановки): `*`, `%`;


### Wildcard *

- `*` searches your filesystem for matching filenames;
- `*` may be used in the target, prerequisites, or in the
  `wildcard` function;
- `*` can't be directly used in a variable definitions;
- when `*` matches no files, it is left as it is (unless run
  in the `wildcard` function)

[EXAMPLE]
```make
print: $(wildcard *.c)
    ls -la $?
```

[BEST PRACTICE] wrap `*`, `%` in the `wildcard` function
[EXAMPLE]
```make
thing_wrong := *.o             # Bad, '*' will not get expanded
thing_right := $(wildcard *.o) # Nice!

all: one two three four

# Fails, because $(thing_wrong) is the string "*.o"
one: $(thing_wrong)

$ Stays as *.o if there are no files that match this pattern
two: *.o

# Works as you would expect: in this case, it does nothing
three: $(thing_right)

# Same as rule 'three'
four: $(wildcard *.o)
```


### Wildcard %

`%` is most often used in rule definitions and some specific
functions.

- when used in "matching mode", it matches one or more
  characters in a string; it's called the **stem**
- when used in "replacing mode", it takes the stem that was
  matched and replaces the in a string;


----------------------------------------------------------------------
## Automatic Variables

There are many
[automatic variables in GNU Make](https://www.gnu.org/software/make/manual/html_node/Automatic-Variables.html),
but just some of them we use often

[EXAMPLE] automatic variables
```make
hey: one two
    # Outputs "hey", since this is the target name
    echo $@

    # Outputs all prerequisites newer than the target
    echo $?

    # Outputs all prerequisites
    echo $^

    touch hey

one:
    touch one
two:
    touch two

clean:
    rm -f hey one two
```


----------------------------------------------------------------------
## Rules (advanced)

The list of _"implicit"_ rules:
- Compiling a C program: `main.c` --> `main.o` by cmd of the
  form `$(CC) -c $(CPPFLAGS) $(CFLAGS) $^ -o $@`
- Compiling a C++ program: `main.cxx` --> `main.o` by cmd of
  the form `$(CXX) -c $(CPPFLAGS) $(CXXFLAGS) $^ -o $@`
- Linking a single object file: `main.o` --> `main` by cmd
  of the form
  `$(CC) $LDFLAGS $^ $(LOADLIBES) $(LDLIBS) -o $@`


The important variables used by implicit rules are:

- `CC`: Program for compiling C programs; default `cc`
- `CXX`: Program for compiling C++ programs; default `g++`
- `CFLAGS`: Extra flags to give to the C compiler
- `CXXFLAGS`: Extra flags to give to the C++ compiler
- `CPPFLAGS`: Extra flags to give to the C preprocessor
- `LDFLAGS`: Extra flags to give to compilers when they are
   supposed to invoke the linker


[EXAMPLE] build a C program without ever explicitly telling
Make how to do the compilation:
```make
CC = gcc     # Flag for implicit rules
CFLAGS = -g  # Flag for implicit rules. Turn on debug info

# Implicit rule #1: main is built via the C linker implicit rule
# Implicit rule #2: main.o is built via the C compilation implicit rule,
	# because main.c exists
main: main.o

main.c:
	echo "int main() { return 0; }" > main.c

clean:
	rm -f main*
```

Так вот, видим правило `main`, у которого в зависимостях
файл `main.o`, здесь имеют место быть неявные правила,
а конкретно автоматическое получение `main.o` из файла
`main.c` при компиляции C. Т.е. _неявно_ будет применена
команда: `$(CC) -c $(CPPFLAGS) $(CFLAGS) $^ -o $@`, где

- `$(СС)` extended as `gcc`
- `$(СPPFLAGS)` extended as `" "`
- `$(СFLAGS)` extended as `-g`
- `$^` extended as `main.o`
- `$(СС)` extended as `main`

[EXAMPLE] implicit rules
```make
CC = gcc     # Flag for implicit rules
CFLAGS = -g  # Flag for implicit rules. Turn on debug info

# Implicit rule #2: main.o is built via the C compilation implicit rule,
# because main.c exists
# Implicit rule #3: main is built via the C linker implicit rule
main: main.o

clean:
	rm -f main*
```


----------------------------------------------------------------------
## Static Pattern Rules

_Static pattern rules_ are another way to write less in
a Makefile, but I'd say are more useful and a bit less
"magic", then _implicit_ rules.

Syntax:
```make
targets...: target-pattern: prereq-patterns...
    cmds
```

The essence is that the given target is matched by the
_target-pattern_ (via a % _wildcard_). Whatever was matched
is called the _stem_. The stem is then substituted into the
_prereq-pattern_, to generate the target's prereqs.

[EXAMPLE] static patter rules (typical use case: .c --> .o)
```make
objects = foo.o bar.o all.o
all: $(objects)

# These files compile via implicit rules
foo.o: foo.c
bar.o: bar.c
all.o: all.c

all.c:
	echo "int main() { return 0; }" > all.c

%.c:
	touch $@

clean:
	rm -f *.c *.o all
```


[EXAMPLE] a little advanced example of static pattern rule
```make
objects = foo.o bar.o all.o
all: $(objects)

# These files compile via implicit rules
# Syntax - targets ...: target-pattern: prereq-patterns ...
# In the case of the first target, foo.o, the target-pattern matches
# foo.o and sets the "stem" to be "foo".
# It then replaces the '%' in prereq-patterns with that stem
$(objects): %.o: %.c

all.c:
	echo "int main() { return 0; }" > all.c

%.c:
	touch $@

clean:
	rm -f *.c *.o all
```


----------------------------------------------------------------------
## Static Pattern Rules and Filter

We can use the `filter` function to define the correct
matches in wildcard.

[EXAMPLE]
```make
obj_files = foo.result bar.o lose.o
src_files = foo.raw bar.c lose.c

all: $(obj_files)
# Note: PHONY is important here. Without it, implicit rules will try
# to build
# the executable "all", since the prereqs are ".o" files.
.PHONY: all 

# Ex 1: .o files depend on .c files.
$(filter %.o,$(obj_files)): %.o: %.c
	@echo "target: $@ prereq: $<"

# Ex 2: .result files depend on .raw files.
$(filter %.result,$(obj_files)): %.result: %.raw
	@echo "target: $@ prereq: $<" 

%.raw:
	@echo "raw file: " $@
	@touch $@

%.c:
	@echo "c file: " $@
	@touch $@


clean:
	rm -f $(src_files)
```

----------------------------------------------------------------------
## Pattern Rules

[EXAMPLE] pattern rule
```make
# Define a patter rule that compiles every .c file into a .o file
%.o: %.c
	$(CC) -c $(CFLAGS) $(CPPFLAGS) $< -o $@
```

- Pattern rules contain a `%` in the target. This `%` matches
any nonempty string, and the other characters match
themselves;
- `%` in a prerequisite of a pattern rule stands
for the same stem that was matched by the `%` in the target;

[EXAMPLE]
```make
# Define a pattern rule that has no pattern in the prerequisites.
# This just creates empty .c files when needed (old or
# doen't exists).
%.c:
   touch $@
```


----------------------------------------------------------------------
## Command Echoing / Silencing

- Add an `@` before a command to stop it from being printed;
- You can also run `$ make -s` to add an `@` before each
  line;

[EXAMPLE] stop cmds being printed
```make
all:
    @echo "This make line will not be printed"
    echo "But this will"
```


----------------------------------------------------------------------
## Command Execution

- **Each command is run in a new shell**

[EXAMPLE] each separate cmd doesn't affect to other
```make
all: 
	cd ..
    # The cd above does not affect this line, because each
    # command is effectively run in a new shell
	echo `pwd`

	# This cd command affects the next because they are on the same line
	cd ..;echo `pwd`

	# Same as above
	cd ..; \
	echo `pwd`
```


----------------------------------------------------------------------
## Flavors and modification

- `=` recursive assignment
- `:=` simple assignment

[EXAMPLE] recursive vs. simple assignments
```make
# Recursive variable. This will print "later" below
one = one ${later_variable}
# Simply expanded variable. This will not print "later" below
two := two ${later_variable}

later_variable = later

all:
	echo $(one)
	echo $(two)
```


> An undefined variable is actually an empty string!


----------------------------------------------------------------------
## Other

- `+=` to append
- command line options (override)
- multiple cmds as one `define` and `endif`
- target-specific variables `all: one = huy`

- conditional `if`, `else`, `endif`
- check if variable is empty (`ifeq`)
- check if a variable is defined (`ifdef`)

- `$(MAKEFLAGS)`


----------------------------------------------------------------------
## Functions

- functions are mainly use for text processing;
- syntax: `$(fn, args)` or `${fn, args}`;
- [builtin functions](https://www.gnu.org/software/make/manual/html_node/Functions.html);

[EXAMPLE] output: I am totally superman
```make
bar := ${subst not, totally, "I am not superman"}
all:
    @echo $(bar)
```

[EXAMPLE] replacing spaces by commas
```make
comma := ,
empty :=
space := $(empty) $(empty)

str := a b c d e
after := ${subst $(space),$(comma),$(str)}

def:
	@echo $(after)
```


> Do **NOT** include spaces in the arguments after the first. See
  next example.

[EXAMPLE] do not include extra spaces
```make
comma := ,
empty :=
space := $(empty) $(empty)

str := a b c d e
after := ${subst $(space),$(comma),$(foo)}

def:
    @echo $(after) # output: ,a ,b ,c ,d ,e
```

- _string substitution_ `${patsubst pattern,replacement,text}`
- _foreach_ function `${foreach pattern,text,replacement}`
- _if_ function `${if cond,true,false}`
- _call_ function
- _shell_ function


----------------------------------------------------------------------
## Other Features

- _include_ Makefile `include filinames...` (`$ make -M`)
- _Vpath Directive_
- _multiline_ (\\)
- `.phony` (if target has a `.phony` make <target> and file <target>
  exists make still will run <target>); use it for every `all` and
  `clean` targets
- `.delete_on_error`
