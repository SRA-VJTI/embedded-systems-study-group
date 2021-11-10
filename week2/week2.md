# Embedded Systems Study Group

- [Embedded Systems Study Group](#embedded-systems-study-group)
	- [Basics of Embedded Programming](#basics-of-embedded-programming)
		- [Working of C compiler](#working-of-c-compiler)
			- [Preprocessors](#preprocessors)
			- [Compiling](#compiling)
			- [Assembly](#assembly)
			- [Linking](#linking)
		- [Header files](#header-files)
		- [Object and source files in C: .o and .c](#object-and-source-files-in-c-o-and-c)
		- [Brief overview of GNU Make and CMake build systems](#brief-overview-of-gnu-make-and-cmake-build-systems)
			- [GNU Make](#gnu-make)
			- [Ninja](#ninja)
			- [CMake](#cmake)
		- [Memory allocation in C](#memory-allocation-in-c)
			- [Static](#static)
			- [Automatic](#automatic)
			- [Dynamic](#dynamic)
	- [Assignment](#assignment)

## Basics of Embedded Programming

### Working of C compiler

#### Preprocessors

The C Preprocessor is not a part of the compiler, but is a separate step in the compilation process. In simple terms, a C Preprocessor is just a text substitution tool and it instructs the compiler to do required pre-processing before the actual compilation. 

All preprocessor commands begin with a hash symbol (#). It must be the first nonblank character, and for readability, a preprocessor directive should begin in the first column. The following section lists down all the important preprocessor directives. They are often called macros.

- macros are very important tools in C/C++
- Consider them to be simple copy paste utility.
- Well, macros are a text processing feature. What happens once you build your program is that all occurrences of macros are “expanded” and replaced by the macro definitions.
- we define macro using `#define <identifier> <value or function definition>`
- So, what happens is, say we have `#define maxvalue 3`, so if in my code, I write maxvalue anywhere, it will be simply replaced by the value, so in this case `3`

```c
#include <stdio.h>

#define AGE 23

int main(int argc, char *argv[])
{
	printf("%d\n", AGE);
}
```

So, it is as good as writing:

```c
#include <stdio.h>

int main(int argc, char *argv[])
{
	printf("%d\n", 23);
}
```

23 got copied in place of AGE. below is a example of a function

```c
#include <stdio.h>

#define SUM(x, y) (x + y)

int main(int argc, char *argv[])
{
	int a = 5;
	int b = 10;
	int sum = SUM(a, b);
	printf("%d\n", sum);
}
```

So, it is as good as writing:

```c
#include <stdio.h>

int main(int argc, char *argv[])
{
	int a = 5;
	int b = 10;
	int sum = (a + b);
	printf("%d\n", sum);
}
```

- to see this in action we can pass `-E` option to the gcc compiler, it will output the processed file.
- Can someone predict the output of `main.c`.
- Now run the command `gcc -E main.c -o main_processed.c` check the newly created file, ignore large list of extra lines add, go to the bottom. We can see SUM and AGE have been replaced by their values.
- Using macros can be extremely unsafe and they hide a lot of pitfalls which are very hard to find. However, as a C or C++ programmer, inevitably, you will encounter macros in your coding life. Even if you don’t use them in your own project, there is a high chance you will encounter them somewhere else, such as a library.

GCC also has a functionality such that we can pass values of macros at compile time, using `-D` parameter to the compiler. Lets consider the following code

```c
#include <stdio.h>

int main(int argc, char *argv[])
{
	printf("%d\n", AGE);
}
```

- Now if you try to compile this you'll get a error, since AGE is undefined. So, now lets try this functionality. Compile using the following command: `gcc main.c -o main -DAGE=32`, now if you run the compiled program, you can see that it will print `32` without any error !! how did this happen ????? 
- So, with `-D` paramter I passed the macro `AGE` to the compiler and told it to replace `AGE` with value `32`, we can see this happening actually using the following command, `gcc -E main.c -o main_processed.c -DAGE=32`. 
- You will see `AGE` is replaced with 32 in the processed code.

#### Compiling

In this step the source code written in C is converted to its appropriate assembly code. So for an ARM processor the assembly code is different from that of x86 processors. 

Compiling is the second step. It takes the output of the preprocessor and generates assembly language, an intermediate human readable language, specific to the target processor.

Generally file format of asm files is `.s` or `.asm`, and it varies according to system used.

So, lets convert our code to assembly. So for this we can use `-S` argument of the gcc compiler. Copy the following code to main.c

```c
#include <stdio.h>

#define AGE 23

int main(int argc, char *argv[])
{
	printf("%d\n", AGE);
}
```

Use the following command to compile the C code into assembly: `gcc -S main.c -o main_assembly.s`

You will get the output in the assembly file something like this

```x86asm
	.file	"main.c"
	.text
	.section	.rodata
.LC0:
	.string	"%d\n"
	.text
	.globl	main
	.type	main, @function
main:
.LFB0:
	.cfi_startproc
	endbr64
	pushq	%rbp
	.cfi_def_cfa_offset 16
	.cfi_offset 6, -16
	movq	%rsp, %rbp
	.cfi_def_cfa_register 6
	subq	$16, %rsp
	movl	%edi, -4(%rbp)
	movq	%rsi, -16(%rbp)
	movl	$23, %esi
	leaq	.LC0(%rip), %rdi
	movl	$0, %eax
	call	printf@PLT
	movl	$0, %eax
	leave
	.cfi_def_cfa 7, 8
	ret
	.cfi_endproc
.LFE0:
	.size	main, .-main
	.ident	"GCC: (Ubuntu 9.3.0-10ubuntu2) 9.3.0"
	.section	.note.GNU-stack,"",@progbits
	.section	.note.gnu.property,"a"
	.align 8
	.long	 1f - 0f
	.long	 4f - 1f
	.long	 5
0:
	.string	 "GNU"
1:
	.align 8
	.long	 0xc0000002
	.long	 3f - 2f
2:
	.long	 0x3
3:
	.align 8
4:
```

- Not getting into details of assembly, but this is a line worth noting: `movl $23, %esi`.  
- See how the C code has to print 23 and then you see the number 23 here, So in this code 23 is being moved in a register, so that it can be displayed on the screen.

#### Assembly

Assembly is the third step of compilation. The assembler will convert the assembly code into pure binary code or machine code (zeros and ones). This code is also known as object code.

So, now we generated assembly code in previous step, now we will compile it into a form that CPU can understand that is binary. We can use `-c` command line argument to generate a binary from asm.

So, run the following command: `gcc -c main_assembly.s -o main.o`

If you try to run main.o it won' run, the reason being we haven't yet completed the compilation process.

#### Linking

Linking is the final step of compilation. The linker merges all the object code from multiple modules into a single one. If we are using a function from libraries, linker will link our code with that library function code.

In static linking, the linker makes a copy of all used library functions to the executable file. In dynamic linking, the code is not copied, it is done by just placing the name of the library in the binary file.

In layman terms, gcc does something very smart, since there can be thousands of .c files in a program, what it does is compile each .c file into a object (.o) file, and then joins the various .o files to make a executable.

So, in the above program we only have a single .c file then why did we need to link it, reason being, we have used stdio, which has printf function, this function is defined in some source file somewhere in the system, so to use printf we need to link the object file of that source file with ours. We do so by using the following command, inshort compile it using gcc, inshort generates a binary executable: `gcc main.o -o main`

Now, we can run the generated binary: `./main` and then output will be `23`.

### Header files

- As we all know that files with .h extension are called header files in C. These header files generally contain function declarations which we can be used in our main C program, like for e.g. there is need to include stdio.h in our C program to use function printf() in the program.

**header files are simply files in which you can declare your own functions that you can use in your main program or these can be used while writing large C programs.**

- .h or header files have function declarations only or macro definition.
- .c or source file contain definition of the functions declared in .h files.

- We do this to break our program into small parts, makes it easy to maintain and find bugs, for example in a library management app, we would put all the data storage functions in one header file and display functions in another, as they both have different tasks. Breaks the program into logical CHUNKS.
- Whatever we define in .h files, and then if we include it in main.c only things defined there are visible globally, if define a variable in a .c file, we cannot see the variable in main.c

### Object and source files in C: .o and .c

- we learnt about .c and .h files, so what are .o files ??
- .o file means `object file`, Contains the compiled contents of the corresponding .c program, compiled as in assembly form.
- Lets take example described [here](../assets/week2/code1.zip), Download the zip and cd into the extracted directory.  
- The command to compile was `gcc main.c library.c`, this was as we need to combine function in library.c and main.c for the program to run, if we only compile main.c, there's only declaration of `welcome_user`, but a definiton is missing, i.e. compiler doesn't know what it needs to do when `welcome_user` function is called. So we need to link these to files together. `gcc main.c library.c` automatically does that for us.
- Other way to do this is compile the respective .c files in its object file and then link them together.

- Example: `gcc -c main.c` you will see a main.o file, it contains compiled contents of main.c, open it you will see gibberish
- `gcc -c library.c` you will see library.o file, again gibberish
- Now, these two .o files contain functions compiled in binary form, we need them to link together for them to work so we link them. we call this process linking
- `gcc main.o library.o -o main` now we can see we passed both the .o files to the compiler, it merged them and formed a single binary, same process take place even for 1000s of files.

### Brief overview of GNU Make and CMake build systems

#### GNU Make

detailed tutorial: https://opensource.com/article/18/8/what-how-makefile

- **Make** is a build automation tool that automatically builds executable programs and libraries from source code by reading files called Make files which specify how to derive the target program.
- A **makefile** is a special file, containing shell commands, that you create and name it as makefile (or Makefile depending upon the system). These rules tell the system what commands you want to be executed. Most times, these rules are commands to compile(or recompile) a series of files
- We define rules about what how make should generate bash commands to execute required functionality

- below is the syntax of a typical rule:

```makefile
target: prerequisites
<TAB> recipe
```

- target is just a name of the task, prerequisites is the files or anything necessary to complete that task, recipe is the actual process that must be executed to complete the task, it contains the actual command line commands. recipe is printed as well as executed, so add a @ before the command to suppress it

- Basic Example: Just prints hello world

```makefile
say_hello:
        echo "Hello World"
```

command will not be displayed

```makefile
say_hello:
        @echo "Hello World"
```

- As an example, a target might be a binary file that depends on prerequisites (source files). On the other hand, a prerequisite can also be a target that depends on other dependencies, as we saw if we use multiple .c file we need to generate the corresponding .o files to generate the binary.
- If multiple rules in a makefile, first one is the default one and executed on calling `make`
- theres's a thing called phony targets, Good examples for this are the common targets "clean" and "all". Chances are this isn't the case, but you may potentially have a file named clean in your main directory. In such a case Make will be confused because by default the clean target would be associated with this file and Make will only run it when the file doesn't appear to be up-to-date with regards to its dependencies. remember we do `make clean` or `make flash` in esp idf. these are different targets. we use `.PHONY = <name of target>` to declare phony tasks
- let's write a makefile to compile the code in earlier section.

```makefile
.PHONY = clean
```

Added the clean task as a phony task

```makefile
main: library main
		@echo "linking main.o and library.o and generating binary"
		gcc -o library library.o main.o
```

main is the task name, and it is the default task. library and main are prerequisite tasks, need to be run before we can run this task. Since generating a binary needs .o files, so it will call the respective tasks which will generate .o files for the given .c files.

```makefile
library: library.c
		@echo "compiling library.c into .object file"
		gcc -c library.c
```

```makefile
main: main.c
		@echo "compiling main.c into .object file"
		gcc -c main.c
```

library and main tasks generates .o files, as we can see the command is `gcc -c main.c`

```makefile
clean: 
		@echo "cleaning build files"
		rm main.o library.o library
```

This task is called by `make clean` and cleans all the generated .o and binary files

- So now we can see library.o main.o and library files are generated only on executing `make`
- Make is more of a general tool, but not specifically for any language, next we see CMake, which is more C++ agnostic, it has certain very useful features.

Following is the code for compiling the code:

```makefile
.PHONY = clean

main: library.o main.o
		@echo "linking main.o and library.o and generating binary"
		gcc -o library library.o main.o

library.o: library.c
		@echo "compiling library.c into .object file"
		gcc -c library.c

main.o: main.c
		@echo "compiling main.c into .object file"
		gcc -c main.c

clean: 
		@echo "cleaning build files"
		rm main.o library.o library
```

#### Ninja 

Ninja is another tool used for generating executable files according to the rules defined in corresponding CMake file. The main purpose behind the creation of 
Ninja is to increase the speed and improve change monitoring methods while generating executable files from large projects(It is reported to be much faster than 
good old Make for many common scenarios). At present, it is used by many popular open source projects such as Google Chrome, LLVM, Android, etc. 

##### Basic specifications : 

Ninja evaluates a graph of dependencies between files, and runs whichever commands are necessary to make your build target up to date as determined by file 
modification times. Conceptually, build statements describe the dependency graph of your project, while rule statements describe how to generate the files along a given edge of the graph.

Here’s a basic .ninja file that demonstrates most of the syntax. 
```ninja
cflags = -Wall

rule cc
  command = gcc $cflags -c $in -o $out

build foo.o: cc foo.c
```
 - Variables : 

Despite the non-goal of being convenient to write by hand, to keep build files readable (debuggable), Ninja supports declaring shorter reusable names for strings. 
A declaration like the following
```ninja
cflags = -g
```
can be used on the right side of an equals sign, dereferencing it with a dollar sign, like this :
```ninja
rule cc
  command = gcc $cflags -c $in -o $out
```
Variables can also be referenced using curly braces like ${in}.

 - Rules : 

Rules declare a short name for a command line. They begin with a line consisting of the rule keyword and a name for the rule. Then follows an indented set of 
variable = value lines.

The basic example above declares a new rule named cc, along with the command to run. In the context of a rule, the command variable defines the command to run, 
$in expands to the list of input files (foo.c), and $out to the output files (foo.o) for the command.

 - Build statements : 

Build statements declare a relationship between input and output files. They begin with the build keyword, and have the format build outputs: rulename inputs. 
Such a declaration says that all of the output files are derived from the input files. When the output files are missing or when the inputs change, Ninja will run 
the rule to regenerate the outputs.

The basic example above describes how to build foo.o, using the cc rule.

A build statement may be followed by an indented set of key = value pairs, much like a rule. These variables will shadow any variables when evaluating the 
variables in the command. For example:

```ninja
cflags = -Wall -Werror
rule cc
  command = gcc $cflags -c $in -o $out

# If left unspecified, builds get the outer $cflags.
build foo.o: cc foo.c

# But you can shadow variables like cflags for a particular build.
build special.o: cc special.c
  cflags = -Wall

# The variable was only shadowed for the scope of special.o;
# Subsequent build lines get the outer (original) cflags.
build bar.o: cc bar.c
```

Lets create the .ninja file for generating executable file from the same folder used in Make's 
example.

```ninja 
rule cc
  command = gcc -c $in -I./include/ -o $out

rule ll
  command = gcc -o $out $in 

rule clean 
  command = rm -rf $in .ninja_deps .ninja_log
```

We start by defining required rules. Below is a short explanation for each rule :-

1. cc : Used for generating .o files from .c files. Note that this command is also using the location of header files.

2. ll : Used for linking .o files

3. clean : Used for deleting all generated files.

```ninja 
build library.o: cc library.c

build main.o: cc main.c

build library: ll library.o main.o

build clean_files: clean library library.o main.o
```

Once all basic rules are defined, we move ahead by using them in build statements as per our 
convenience. Below is a short explanation for each build statement :-

1. build library.o : Generates library.o from library.c using 'cc' rule.

2. build main.o : Generated main.o from main.c using 'cc' rule.

3. build library : Generates the executable 'library' after linking 'main.o' and 'library.o' using 'll' rule.

4. build clean_files : Deletes all generated files using 'clean' rule.

The complete build.ninja file for generating required executable is : 

```ninja
rule cc
  command = gcc -c $in -I./include/ -o $out

rule ll
  command = gcc -o $out $in 

rule clean 
  command = rm -rf $in .ninja_deps .ninja_log

build library.o: cc library.c

build main.o: cc main.c

build library: ll library.o main.o

build clean_files: clean library library.o main.o
```

For more information, you can refer the official manual [here](https://ninja-build.org/manual.html#_introduction).

#### Note : 
There are many other build file(s) generating tools apart from Make and Ninja such as Boost's b2
(Boost.build), SCons, etc. which have many similarities and would be easier to use once you learn 
related basics. For the sake of brevity, we have only included the most popular build systems and 
you are encouraged to explore others.

#### CMake

CMake is an extensible, open-source system that manages the build process in an operating system and in a compiler-independent manner.  

- CMake is much more high-level. It's tailored to compile C++, for which you write much less build code, but can be also used for general purpose build. make has some built-in C/C++ rules as well, but they are mostly useless.

- CMake does a two-step build: it generates a low-level build script in ninja or make or many other generators, and then you run it. All the shell script pieces that are normally piled into Makefile are only executed at the generation stage. Thus, CMake build can be orders of magnitude faster.

- The grammar of CMake is much easier to support for external tools than make's.

[Content](https://tuannguyen68.gitbooks.io/learning-cmake-a-beginner-s-guide/content/chap1/chap1.html)

Well Cmake is a build system generator which is used to generate projects over different platforms whether its be Linux, MacOS or Windows. Cmake is known as build system generator because it can generate projects using different available compilers like GCC , Clang and MSVC. CMake is able to do so because it has its own domain specific language (DSL) which allows us to generate platform-native build systems with the same set of CMake scripts. CMake scripts are always written in a file named as CMakeLists.txt . The CMake software toolset gives developers full control over the whole life cycle of a given project:

- CMake let’s you describe how your project, whether building an executable, libraries, or both, has to be configured, built, and installed on all major hardware and operating systems.
- CTest allows you to define tests, test suites, and set how they should be executed.
- CPack offers a DSL for all your packaging needs, whether your project should be bundled and distributed in source code or platform-native binary form.
- CDash will be useful for reporting the results of tests for your project to an online dashboard.

Just like GNU Make had a file where can describe the steps of compilation, similarly we have to write a cmake file. We will use cmake to compile code in the above example.

Download the following [zip](../assets/week2/code2.zip), extract it and cd into the folder.

Following is the CMakeLists.txt present in the folder

```cmake
# Specify the minimum version for CMake
cmake_minimum_required(VERSION 2.8)

# Project's name
project(hello)

# Set the output folder where your program will be created
set(CMAKE_BINARY_DIR ${CMAKE_SOURCE_DIR}/bin)
set(EXECUTABLE_OUTPUT_PATH ${CMAKE_BINARY_DIR})

# The following folder will be included
include_directories("${PROJECT_SOURCE_DIR}/include")

# add the files which are needed to generate the binary and also the name of the binary
add_executable(library ${PROJECT_SOURCE_DIR}/main.c ${PROJECT_SOURCE_DIR}/string_add.c ${PROJECT_SOURCE_DIR}/library.c)
```

Now to compile we follow the following steps:

Using Make : 
```bash
mkdir build
cd build
cmake ..
make
```

Using Ninja :
```bash
mkdir build
cd build
cmake -G Ninja .. 
ninja
```

Now you can see in the root directory that a `/bin` folder has been generated, it contains the generated binary. Run and see it work.

### Memory allocation in C

#### Static

Static allocation is what happens when you declare a static or global variable. Each static or global variable defines one block of space, of a fixed size. The space is allocated once, when your program is started (part of the exec operation), and is never freed.

```c
#include<stdio.h> 
int fun() 
{ 
  static int count = 0; 
  count++; 
  return count; 
} 
   
int main() 
{ 
  printf("%d ", fun()); 
  printf("%d ", fun()); 
  return 0; 
}
```

If you run this program, you will see something unexpected.

#### Automatic

Automatic allocation happens when you declare an automatic variable, such as a function argument or a local variable. The space for an automatic variable is allocated when the compound statement containing the declaration is entered, and is freed when that compound statement is exited.

Example of this allocation is when we declare the size of an array during run time. Inshort compiler doesn't know the actual size of array during compile time

Note that that the size of the array is declared (and known) before the declaration of the array:

```c
#include <stdio.h>

int main()
{
    int number_of_elems = 0;
    printf("enter number of elems: ");
    scanf("%d", &number_of_elems);
    
    char arr[number_of_elems];
    
    printf("size: %lu", sizeof(arr));
    return 0;
}
```

The compiler now inserts assembler code to reserve space on the stack for the array, something like (pseudo assembler):

```x86asm
add sp, number_of_elems*sizeof_int
```

#### Dynamic

Dynamic memory allocation is a technique in which programs determine as they are running where to store some information. You need dynamic allocation when the amount of memory you need, or how long you continue to need it, depends on factors that are not known before the program runs.

For example, you may need a block to store a line read from an input file; since there is no limit to how long a line can be, you must allocate the memory dynamically and make it dynamically larger as you read more of the line.

Or, you may need a block for each record or each definition in the input data; since you can’t know in advance how many there will be, you must allocate a new block for each record or definition as you read it.

When you use dynamic allocation, the allocation of a block of memory is an action that the program requests explicitly. You call a function or macro when you want to allocate space, and specify the size with an argument. If you want to free the space, you do so by calling another function or macro. You can do these things whenever you want, as often as you want.

Dynamic allocation is not supported by C variables; there is no storage class “dynamic”, and there can never be a C variable whose value is stored in dynamically allocated space. The only way to get dynamically allocated memory is via a system call (which is generally via a GNU C Library function call), and the only way to refer to dynamically allocated space is through a pointer. Because it is less convenient, and because the actual process of dynamic allocation requires more computation time, programmers generally use dynamic allocation only when neither static nor automatic allocation will serve.

For example, if you want to allocate dynamically some space to hold a struct foobar, you cannot declare a variable of type struct foobar whose contents are the dynamically allocated space. But you can declare a variable of pointer type struct foobar * and assign it the address of the space. Then you can use the operators ‘*’ and ‘->’ on this pointer variable to refer to the contents of the space:

```c
{
  struct foobar *ptr
     = (struct foobar *) malloc (sizeof (struct foobar));
  ptr->name = x;
  ptr->next = current_foobar;
  current_foobar = ptr;
}
```

## Assignment 

* [Assignment 2](../assets/week2/embedded_assignment_2.pdf)
