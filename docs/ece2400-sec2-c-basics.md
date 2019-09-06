
Section 2: Compiling and Running C Programs
==========================================================================

This section serves as gentle introduction to the basics of compiling and
running C programs on the `ecelinux` machines.

1. The ecelinux Machines
--------------------------------------------------------------------------

Follow the same process as in the last section.

 - login to a workstation with your NetID and password
 - use MobaXterm to log into the `ecelinux` servers
 - make sure you source the setup script
 - verify ECE2400 is in your prompt

Now clone the GitHub repo we will be using in this section using the
following commands:

```bash
 % mkdir -p ${HOME}/ece2400
 % cd ${HOME}/ece2400
 % git clone git@github.com:cornell-ece2400/ece2400-sec2 sec2
 % cd sec2
 % cat README.md
```

2. Using Online Tools to Explore C Programs
--------------------------------------------------------------------------

In this section, we will explore to online tools that can be useful when
exploring C programs. The first is Repl.it which enables us to write,
compile, and run C programs in the cloud, and the second is Compiler
Explore which enables us to compile C programs and view the corresponding
machine instructions in the cloud.

### 2.1. Repl.it

Repl.it is a free online service which supports writing, compiling, and
executing C/C++ programs completely online in your browser. It will
enable us to quickly explore small C/C++ code snippets. Start a browser
and go to <https://Repl.it>. You can use Repl.it without an account. You
can always create an account later. Click _new repl_ in the upper
right-hand corner. In the _Search for a language_ drop-down choose C and
the click _Create repl_. You will see a default program. Replace ths
default program with the folllowing program inspired by today's lecture.

    #include <stdio.h>

    int avg( int x, int y )
    {
      int sum = x + y;
      return sum / 2;
    }

    int main()
    {
      int a = 10;
      int b = 20;
      int c = avg( a, b );
      printf( "average of %d and %d is %d\n", a, b, c );
      return  0;
    }

Click _run_. This will compile and execute your C program in the cloud
and then display the output on the right.

!!! note "To-Do On Your Own"

    Change the value of `b` to 15. Recompile and execute your C program.
    Is the result what you expect? Modify the `avg` function to find the
    average of three values instead of just two. Modify the `main`
    function to properly call your updated function and verify it
    calculates the average correctly.

We will be using Repl.it throughout the course. We strongly encourage
students to experiment with Repl.it frequently. The best way to truly
understand computer systems programming with C and C++ is to actually
write programs, and the easiest way to write C and C++ programs is to
just use Repl.it!

### 2.2. Compiler Explorer

Compiler Explore is a free online servce that compiles C programs to
machine instructions and then displays these machine instructions for you
to inspect completely online in your browser. It will enable us to
quickly explore the connection between C and C++ code and the low-level
machine instructions actually executed by the computer system. Start a
browser and go to <https://godbold.org>. You will see a default code
snippet. Replace this code with the following code inspired by today's
lecture.

    int avg( int x, int y )
    {
      int sum = x + y;
      return sum / 2;
    }

Compiler Explore will automatically display the corresponding machine
instructions on the right. It color codes C code on the left to match
which exact machine instructions are produced by that C code on the
right. You can right click on an instruction and select _View opcode doc_
to learn more about what each instruction does. You can also choose from
many different compilers targetting many different instruction set
architectures (ISAs). Here are three different compiler/ISAs you might
want to experiment with:

 - x86-64 clang 8.0.0 : This is similar to the compiler on ecelinux
 - MIPS gcc 5.4 (el) : This is similar to what is used in ECE 2400
 - RISCV-V rv32gc clang : This is similar to what is used in ECE 2400

Go ahead and choose x86-64. Notice that even though the C source code
uses the integer division operator there is no division instruction on
the right. This is because the compiler is smart enough to figure out
that dividing by 2 is the same as shifting to the right by one bit. In th
eopper-right corner you will see `-O3`. This is a command line option
that tells the compiler to apply as many optimizations as it can. Delete
`-O3` and take a look at the resulting machine instructions. First,
notice that the compiler has generated many more instructions. Second,
notice that it now is using a division instruction (`idiv`). The
unoptimized code would execute far slower than the optimized code.

!!! note "To-Do On Your Own"

    Copy-and-paste the three-input average program you developed earlier
    into Compiler Explore. Inspect the corresponding machine instructions
    for all three compiler/ISA options mentioned above with and without
    optimizations.

3. C Preprocessor
--------------------------------------------------------------------------

Before we can understand how to write and compile C programs, we need to
understand the C preprocessor. The preprocessor takes an input C source
file, preprocesses it, and generates the preprocessed version of the C
source file. It is important to realize that the C preprocesor is not
really part of the C programming language. The C preprocessor simply
manipulates the text in the C source files and knows nothing about the C
programming language's syntax or semantics. The C preprocessor is
powerful but also very easy to abuse. Using the C preprocessor can cause
subtle bugs and is usually not necessary. Unfortunately, there are a few
cases where we have no choice but to use the C preprocessor, so we must
learn at least the basics.

The best way to understand the C preprocessor is actually to use it to
preprocess standard text files as opposed to C source files. Assume we
wish to create a text file which includes both warm and cool colors. We
could simply copy-and-paste our lists from `warm-colors.txt` and
`cool-colors.txt`, but this would be redundant and difficult to maintain
if we wanted to add a new warm or cool color. We can instead use the C
preprocessor to _include_ the contents of one text file in another text
file. Use Micro (or Geany) to create a new file named `colors-in.txt`
like this:

```bash
 % cd ${HOME}/ece2400/sec2
 % micro colors-in.txt
```

Put the following content into this file:

    #include "warm-colors.txt"
    #include "cool-colors.txt"

Recall that you can save a file from Micro using Ctrl-S and you can quit
using Ctrl-Q.

The C preprocessor copies the input source file to the output source
file, while also looking for _C preprocessor directives_. All C
preprocessor directives begin with the special `#` character. The
`#include` directive specifies the file name of a different text file to
include. The file name should be be specified using double quotes (`""`).

Now we can use the C preprocessor (`cpp`) to preprocess the `-in.txt`
files into a final text file that contains both the warm and cool colors.

```bash
% cd ${HOME}/ece2400/sec2
% cpp -o colors.txt colors-in.txt
% cat colors.txt
```

The `-o` command line option is used to specify the name of the output
file. The content of the `colors.txt` should look like this:

    # 1 "colors-in.txt"
    # 1 "<built-in>"
    # 1 "<command-line>"
    # 31 "<command-line>"
    # 1 "/usr/include/stdc-predef.h" 1 3 4
    # 32 "<command-line>" 2
    # 1 "colors-in.txt"
    # 1 "warm-colors.txt" 1
    red
    orange
    yellow
    # 2 "colors-in.txt" 2
    # 1 "cool-colors.txt" 1
    blue
    green
    purple
    # 2 "colors-in.txt" 2

xThe C preprocessor has included all of the colors in a single output
file, but it has also included some additional lines beginning with the
`#` character to specify information about where all of the pieces of
text originally came from. We can tell `cpp` to not include this extra
metadata with the `-P` command line option.

```bash
 % cd ${HOME}/ece2400/sec2
 % cpp -P -o colors.txt colors-in.txt
 % cat colors.txt
```

This example illustrates the first way we will use the C preprocessor. We
will use the `#include` directive to include common C source files in
several of our own C source files. This approach avoids redundancy and
makes our programs much easier to maintain since we can make changes in a
single C source file, and those changes can be immediately reflected in
any program which includes that C source file. We have actually already
seen this use of the C preprocessor earlier in this section when we
included the `stdio.h` header file which includes the declaration of the
`printf` function.

We will also use the C preprocessor to create _include guards_. An
include guard is a way to ensure that the contents of a given file is
only inserted into the output file once, even if we include it multiple
times. For example, modify `warm-colors.txt` using Micro (or Geany) as
follows:

    #ifndef WARM_COLORS_TXT
    #define WARM_COLORS_TXT
     red
     orange
     yellow
    #endif

The `#ifndef` directive is a conditional which will only include the
content after the directive if the given preprocessor macro is defined.
The `#define` directive "defines" the given preprocessor macro. So these
directives essentially check to see if `WARM_COLORS_TXT` is defined and
if not it will include the text in the file ... and _also_ define
`WARM_COLORS_TXT`. If we try and include this same file again then
`WARM_COLORS_TXT` will already be defined and we will skip over the
contents of the file. Modify `cool-colors.txt` as follows:

    #ifndef COOL_COLORS_TXT
    #define COOL_COLORS_TXT
     blue
     green
     purple
    #endif

And now modify `colors-in.txt` to include each file three times:

    #include "warm-colors.txt"
    #include "warm-colors.txt"
    #include "warm-colors.txt"
    #include "cool-colors.txt"
    #include "cool-colors.txt"
    #include "cool-colors.txt"

If you run the C preprocessor you will see that the colors are only
included once because of the include guards.

```bash
 % cd ${HOME}/ece2400/sec2
 % cpp -P -o colors.txt colors-in.txt
 % cat colors.txt
```

!!! note "To-Do On Your Own"

    Remove the include guards and rerun the C preprocessor on
    `colors-in.txt`. Verify that the resulting output file `colors.txt`
    has multiple copies of the warm and cool colors.

4. Compiling and Running a Single-File C Program
--------------------------------------------------------------------------

Now that we have explored how to use the C preprocessor for file
inclusion and include guards, we will can turn out attention to writing C
programs. We will begin by writing a single-file C program to calculate
the average of two integers. Our goal is to reproduce what we did with
Repl.it and Compiler Explorer, but by using various command line tools on
the `ecelinux` machines. Edit the `avg-sfile.c` code to include an
appropriate implementation of the `avg` function.

    #include <stdio.h>

    int avg( int x, int y )
    {
      int sum = x + y;
      return sum / 2;
    }

    int main()
    {
      int a = 10;
      int b = 20;
      int c = avg( a, b );
      printf( "average of %d and %d is %d\n", a, b, c );
      return  0;
    }

We use a _compiler_ to compiler the C source code into an executable
binary (i.e., the actual bits) that the machine can understand. In this
course we will be using the GNU C compiler (`gcc`). Let's go ahead and
give this a try:

```bash
 % cd ${HOME}/ece2400/sec2
 % gcc -Wall -o avg-sfile avg-sfile.c
 % ls
```

The `gcc` command takes as input the C source file to compile and the
command line option `-o` is used to specify the output exectutable binary
(i.e., the file with the machine instructions). We also use the `-Wall`
command line option to report all warnings. Note that `gcc` automatically
takes care of running the C preprocessor for us. After running the `gcc`
command you should see a new `avg-sfile` file in the directory. We can
execute this binary by simply calling it as any other Linux command.

```bash
 % cd ${HOME}/ece2400/sec2
 % ./avg-sfile
```

Recall that a single dot (`.`) always refers to the current working
directory. Essentially we are telling Linux that we want to run the
executable named `avg-sfile` which is located in the current working
directory. Repl.it is basically doing these same steps just in the cloud.

Now let's examine the machine instructions using the `objdump` command.

```bash
 % cd ${HOME}/ece2400/sec2
 % objdump -dC avg-sfile | less
```

The `objdump` command takes an executable binary and shows you the
machine instructions in a human readable format. We are piping it through
`less` so we can scroll through the output. Try and find how many machine
instructions are used to implement the `avg` function. Compiler Explore
is basically doing these same steps just in the cloud. Recall what you
learned from experimenting with Compiler Explore earlier in this section.
Does it seem like the compiler generated optimized code or unoptimized
code? You can exit `less` by pressing the `q` key. Let's recompile our
program with optimizations.

```bash
 % cd ${HOME}/ece2400/sec2
 % gcc -Wall -O3 -o avg-sfile avg-sfile.c
 % objdump -dC avg-sfile | less
```

Now how many machine instructions are used to implement the `avg`
function?

!!! note "To-Do On Your Own"

    Copy-and-paste the three-input average program you developed earlier
    into `avg-sfile.c`. Recompile this program using `gcc`, and then
    execute this program from the command line. Verify that it produces
    the expected output. Inspect the corresponding machine instructions
    with and without optimizations using `objdump`.

5. Compiling and Running a Multi-File C Program
--------------------------------------------------------------------------

Real C programs are almost never contained in a single file. They require
many files which must be individually compiled and then _linked_
together. Linking is the process of merging together different binary
files each with its own set of machine instructions. To illustrate this
process we will break our `avg-sfile.c` source file into two files:
`avg.c` will contain the `avg` function, and `avg-mfile.c` will contain
the `main` function. We will compile each of these files into its own
object file and then link these object files into a complete executable
binary. Here is a figure illustrating the compiler and linker flow.

 ![compiler flow](img/ece2400-sec2-flow.png)

Go ahead and use Micro (or Geany) to create the `avg.c` file:

    #include "avg.h"

    int avg( int x, int y )
    {
      int sum = x + y;
      return sum / 2;
    }

And now create the `avg-mfile.c` file:

    #include <stdio.h>
    #include "avg.h"

    int main()
    {
      int a = 10;
      int b = 20;
      int c = avg( a, b );
      printf( "average of %d and %d is %d\n", a, b, c );
      return  0;
    }

We will use `gcc` compile the `avg.c` source file into the `avg.o` object
file, and we will also use `gcc` to compile the `avg-mfile.c` source file
into the `avg-mfile.o` object file. Finally, we can use `gcc` to _link_
both object files along with the pre-compiled C standard library and any
startup code to produce an executable binary.

We will also need a `header file` named `avg.h`. Header files are the key
to multi-file C programs. The `avg-mfile.c` source file needs to call the
`avg` function, but the `avg` function is in a different source file.
When we compile the `avg-mfile.c` source file, how will the compiler know
that the `avg` function exists to ensure the programmer is not
accidentally calling an undefined function? How will the compiler know
what parameters the `avg` function takes, so it can perform type
checking? The `avg-mfile.c` source file cannot directly include `avg.c`
since that would result in the same function being compiled twice into
two different object files (which would cause a linker error). What we
need to do is have a way to tell `avg-mfile.c` the `avg` _function
prototype_ (i.e., the interface of the function including its name,
parameter list, and return type) but not the `avg` _function
implementation_. We do this with a `function declaration`. A function
definition specifies both the function prototype (interface) and the
implementation at the same time, while a function declaration just
specifies the function prototype without the implementation. A header
file contains all of the function declarations but no function
definitions. All of the function definitions are placed in a source file
that goes along with the header file. If we want to call a function that
is defined in a different source file, then we simply use the `#include`
directive to include the appropriate header file. The linker will take
care of making sure the machine instructions corresponding to every
function definition is linked together into the executable binary. Create
a header file for `avg.c` named `avg.h` with the following contents.

    #ifndef TUT3_C_AVG_H
    #define TUT3_C_AVG_H

    int avg( int x, int y );

    #endif

Notice the include guards implemented using the C preprocessor. Let's go
ahead and compile `avg.c` and `avg-mfile.c` into their corresponding
object files:

```bash
 % cd ${HOME}/ece2400/sec2
 % gcc -Wall -c -o avg.o avg.c
 % gcc -Wall -c -o avg-mfile.o avg-mfile.c
```

We use the `-c` command line option to indicate that `gcc` should create
an _object_ file as opposed to a complete executable binary. An object
file is just a piece of machine instructions. We cannot actually execute
an object file; we need to link multiple object files together to create
a complete executable binary. We usually use the `.o` filename extension
to indicate that these files are object files. Let's use `objdump` to
look inside each of these object files.

```bash
 % cd ${HOME}/ece2400/sec2
 % objdump -dC avg.o
 % objdump -dC avg-mfile.o
```

You should be able to see that each object file only contains a few
machine instructions. The `avg.o` object file only contains machine
instructions that correspond to the `avg` function, while the
`avg-mfile.o` object file only contains machine instructions that
correspond to the `main` function.

Let's link these two object files together to create a complete
executable binary that we can actually run.

```bash
 % cd ${HOME}/ece2400/sec2
 % gcc -Wall -o avg-mfile avg.o avg-mfile.o
 % objdump -dC avg-mfile
```

Notice that the complete executable binary contains all of the machine
instructions for _both_ the `avg` and `main` functions along with a bunch
of additional system-level code (e.g., for the `printf` function). Let's
go ahead and run the executable binary.

```bash
 % cd ${HOME}/ece2400/sec2
 % ./avg-mfile
```

We can simplify this process and do the compilation and linking in a
single step by specifying multiple C source files on a single command
line.

```bash
 % cd ${HOME}/ece2400/sec2
 % gcc -Wall -o avg-mfile avg.c avg-mfile.c
 % ./avg-mfile
```

!!! note "To-Do On Your Own"

    Create a new source file named `avg3.c` that contains the three-input
    average program you developed earlier. Create a header file named
    `avg3.h` which contains just the function prototype for the
    three-input average program. Modify `avg-mfile.c` to call both the
    two-input and the three-input `avg` functions. Try compiling `avg3.c`
    into its own object file named `avg3.o`. Link all three files (i.e.,
    `avg.o`, `avg3.o`, `avg-mfile.o`). You will see a linker error
    indicating that you have two functions with the same name. The
    compiler does not know how to disambiguate these two functions. Name
    the three-input average function `avg3` and update the source file
    (`avg3.c`), header file (`avg.h`), and `avg-mfile.c` appropriate.
    Recompile the object files and try linking the program together
    again. Verify that it produces the expected output.

