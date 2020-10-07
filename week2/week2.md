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
    - [Memory allocation in C](#memory-allocation-in-c)
      - [Static](#static)
      - [Automatic](#automatic)
      - [Dynamic](#dynamic)

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

```C
#include <stdio.h>

#define AGE 23

int main(int argc, char *argv[])
{
	printf("%d\n", AGE);
}
```

So, it is as good as writing:

```C
#include <stdio.h>

int main(int argc, char *argv[])
{
	printf("%d\n", 23);
}
```

23 got copied in place of AGE. below is a example of a function

```C
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

```C
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

```C
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

```C
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
- Lets take example described [here](https://github.com/VedantParanjape/idf-notes-sra/tree/master/01), copy the files to your system, namely main.c, library.c and library.h. 
- The command to compile was `gcc main.c library.c`, this was as we need to combine function in library.c and main.c for the program to run, if we only compile main.c, there's only declaration of `welcome_user`, but a definiton is missing, i.e. compiler doesn't know what it needs to do when `welcome_user` function is called. So we need to link these to files together. `gcc main.c library.c` automatically does that for us.
- Other way to do this is compile the respective .c files in its object file and then link them together.

- Example: `gcc -c main.c` you will see a main.o file, it contains compiled contents of main.c, open it you will see gibberish
- `gcc -c library.c` you will see library.o file, again gibberish
- Now, these two .o files contain functions compiled in binary form, we need them to link together for them to work so we link them. we call this process linking
- `gcc main.o library.o -o main` now we can see we passed both the .o files to the compiler, it merged them and formed a single binary, same process take place even for 1000s of files.

### Brief overview of GNU Make and CMake build systems

### Memory allocation in C

#### Static 
#### Automatic
#### Dynamic