
Section 2: Compiling and Running C Programs
==========================================================================

This section serves as gentle introduction to the basics of compiling and
running C programs on the `ecelinux` machines. The [corresponding
tutorial](ece2400-tut2-c-basics.md) provides significantly more detail
and background.

1. The `ecelinux` Machines
--------------------------------------------------------------------------

Follow the same process as in the last section.

 - login to a workstation with your NetID and password
 - for the discussion section, we will be using screensharing via zoom
 - start zoom, choose from menubar: _Applications > Internet > Zoom_
 - click _"Join a Meeting"_
 - enter _"ece2400"_ in the text box
 - check _"Don't connect to Audio"_
 - check _"Turn off my video"_
 - click _"Join"_
 - click _"View Options"_ at the top
 - choose "Exit Full Screen"_
 - drag window over to the right-hand edge to make it take up the right
    half of the screen

2. Basic Git Usage
--------------------------------------------------------------------------

In this course, we will be using Git as our revision control and source
code management system. We will be using GitHub for centralized online
repository hosting, and TravisCI for online continuous integration
testing. These tools will enable us to adopt an agile hardware
development methodology so your group can rapidly collaborate and iterate
on the design, verification, and evaluation of the assignments. You
should have already setup your GitHub account on `github.com` during the
previous section. Please also make sure you have setup your ssh keys
correctly (see Section 2 of the ECE 2400 Tutorial 2).

Fork'ing a repo means making a copy of that repo for your own local use.
We won't actually be forking repos for the programming assignments, but
it is an easy way for you to grab some example code for the discussion
section. Go to the example repo here:

 - https://github.com/cornell-ece2400/ece2400-sec2

Click on the "Fork" button. Wait a few seconds and then visit the new
copy of this repo in your own person GitHub workspace:

 - `https://github.com/<githubid>/ece2400-sec2`

Where `<githubid>` is your GitHubID. Now let's clone your new repo to the
`ecelinux` machine.

```bash
 % source setup-ece2400.sh
 % cd ${HOME}/ece2400
 % git clone git@github.com:<githubid>/ece2400-sec2 sec2
 % cd sec2
 % cat README.md
```

Where `<githubid>` is your GitHubID. Now let's add some new files to the
repository. Use your favorite text editor (e.g., Geany) to create a file
named `warm-colors.txt` with three warm colors:

```text
 red
 orange
 yellow
```

Now use your favorite text editor again to create a file named
`cool-colors.txt` with three cool colors.

```text
 blue
 green
 purple
```

Now let's add these files to our repository. First use the `git status`
command to check on the status of the repository.

```bash
 % cd ${HOME}/ece2400/sec2
 % git status
```

You should see that git has noticed two "untracked files" which are in
the working directory but are not currently being tracked by git. Let's
"add" these two files to git's "staging" area so it now knows it should
keep track of them:

```bash
 % cd ${HOME}/ece2400/sec2
 % git add warm-colors.txt
 % git add cool-colors.txt
 % git status
```

The status of these two files have changed. Git reports that both of the
new files are ready to be committed. Let's go ahead and commit these
changes into your local repository.

```bash
 % cd ${HOME}/ece2400/sec2
 % git commit -m "add some colors"
```

Note that nothing has happened on GitHub yet. GitHub does _not_ know
anything about these local changes. We need to explicitly "push" our new
commits up to GitHub like this:

```bash
 % cd ${HOME}/ece2400/sec2
 % git push
```

Now go to the repository page on GitHub and verify that there are two new
files. Let's try making a change to this repository through the GitHub
web interface. Click on "Create new file". Name the file `languages.txt`
and add a list of programming languages:

```text
 C
 C++
 Python
 MATLAB
 Java
```

Now click "Commit new file". Verify that there is a new file in the repo
using the GitHub web interface. Now let's "pull" these new changes from
GitHub to your local repo on `ecelinux`:

```bash
 % cd ${HOME}/ece2400/sec2
 % git pull
 % cat languages.txt
```

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
file. Use your favorite text editor to create a new file named
`colors-in.txt` with the following content:

```c
 #include "warm-colors.txt"
 #include "cool-colors.txt"
```

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

```text
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
```

The C preprocessor has included all of the colors in a single output
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
seen this use of the C preprocessor in the previous section when we
included the `stdio.h` header file which includes the declaration of the
`printf` function.

We will also use the C preprocessor to create _include guards_. An
include guard is a way to ensure that the contents of a given file is
only inserted into the output file once, even if we include it multiple
times. For example, modify `warm-colors.txt` as follows:

```text
#ifndef WARM_COLORS_TXT
#define WARM_COLORS_TXT
 red
 orange
 yellow
#endif
```

The `#ifndef` directive is a conditional which will only include the
content after the directive if the given preprocessor macro is defined.
The `#define` directive "defines" the given preprocessor macro. So these
directives essentially check to see if `WARM_COLORS_TXT` is defined and
if not it will include the text in the file ... and _also_ define
`WARM_COLORS_TXT`. If we try and include this same file again then
`WARM_COLORS_TXT` will already be defined and we will skip over the
contents of the file. Modify `cool-colors.txt` as follows:

```text
#ifndef COOL_COLORS_TXT
#define COOL_COLORS_TXT
 blue
 green
 purple
#endif
```

And now modify `colors-in.txt` to include each file three times:

```text
 #include "warm-colors.txt"
 #include "warm-colors.txt"
 #include "warm-colors.txt"
 #include "cool-colors.txt"
 #include "cool-colors.txt"
 #include "cool-colors.txt"
```

If you run the C preprocessor you will see that the colors are only
included once because of the include guards.


```bash
 % cd ${HOME}/ece2400/sec2
 % cpp -P -o colors.txt colors-in.txt
 % cat colors.txt
```

4. Compiling and Running a Single-File C Program
--------------------------------------------------------------------------

Now that we have explored how to use the C preprocessor for file
inclusion and include guards, we will can turn out attention to writing C
programs. We will begin by writing a single-file C program to calculate
the average of two integers. Our goal is to reproduce what we did earlier
by using various command line tools on the `ecelinux` machines instead of
using Compiler Explorer and Repl.it. Edit the `avg-sfile.c` code to
include an appropriate implementation of the `avg` function.

```c
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
```

We use a _compiler_ to compiler the C source code into an executable
binary (i.e., the actual bits) that the machine can understand. In this
course we will be using the GNU C compiler (`gcc`). Let's go ahead and
give this a try:

```bash
 % cd ${HOME}/ece2400/sec2
 % gcc -o avg-sfile avg-sfile.c
 % objdump -dC avg-sfile | less
 % ./avg-sfile
```

The `objdump` command takes an executable binary and shows you the
machine instructions in a human readable format. We are piping it through
`less` so we can scroll through the output. Try and find the machine
instructions that go along with the `avg` function. Then we actually
execute the binary by simply calling it as any other Linux command.
Recall that a single dot (`.`) always refers to the current working
directory. Essentially we are telling Linux that we want to run the
executable named `avg-sfile` which is located in the current working
directory.

5. Compiling and Running a Multi-File C Program
--------------------------------------------------------------------------

Real C programs are almost never contained in a single file. They require
many files which must be individually compiled and then linked together.
To illustrate this process we will break our `avg-sfile.c` source file
into two files: `avg.c` will contain the `avg` function, and
`avg-mfile.c` will contain the `main` function. Go ahead and use your
favorite text editor to create the `avg.c` file:

```c
#include "avg.h"

int avg( int x, int y )
{
  int sum = x + y;
  return sum / 2;
}
```

And now create the `avg-mfile.c` file:

```c
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
```

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

```c
#ifndef TUT3_C_AVG_H
#define TUT3_C_AVG_H

int avg( int x, int y );

#endif
```

Notice the include guards implemented using the C preprocessor. Let's go
ahead and compile `avg.c` and `avg-mfile.c` into their corresponding
object files:

```bash
 % cd ${HOME}/ece2400/sec2
 % gcc -c -o avg.o avg.c
 % gcc -c -o avg-mfile.o avg-mfile.c
```

And now we can link these two object files together to create an
executable binary that we can run:

```bash
 % cd ${HOME}/ece2400/sec2
 % gcc -o avg-mfile avg.o avg-mfile.o
 % ./avg-mfile
```

We can actually simplify this process and do the compilation and linking
in a single step like this:

```bash
 % cd ${HOME}/ece2400/sec2
 % gcc -o avg-mfile avg.c avg-mfile.c
 % ./avg-mfile
```


