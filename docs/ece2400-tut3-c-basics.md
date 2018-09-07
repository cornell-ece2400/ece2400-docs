
Tutorial 3 : Compiling and Running C Programs
==========================================================================

The first few programming assignments for this course will use the C
programming language. In lecture we use Compiler Explorer and Repl.it to
quickly experiment with small C programs, but eventually we need to
actually write and compile C programs on a real machine. This tutorial
discusses how we can use the open-source GNU C compiler (`gcc`) to
compile our C programs on the \TT{ecelinux} machines. We will experiment
with both single-file C programs (simple but not representative of real C
projects) and multi-file programs (more complex but also more realistic).
In this tutorial, we will be running \TT{gcc} directly from the command
line so we can understand each step. In the next tutorial, we will see
how we can use various tools to automate this process. All of the tools
are installed and available on the `ecelinux` machines. This tutorial
assumes that students have completed the tutorial on Linux and Git. We
strongly recommend students also read Chapters 1-6 in the course text
book, _``All of Programming,''_ by A. Hilton and A. Bracy (2015).
Chapters 5-6 are particularly relevant since they discuss the general
process of compiling, testing, and debugging C programs.

To follow along with the tutorial, access the course computing resources,
and type the commands without the `%` character (for the `bash` prompt).
In addition to working through the commands in the tutorial, you should
also try the more open-ended tasks marked _To-Do On Your Own_. Before you
begin, make sure that you have **sourced the setup-ece2400.sh script** or
that you have added it to your `.bashrc` script, which will then source
the script every time you login. Sourcing the setup script sets up the
environment required for this class.

You should start by forking the tutorial repository on GitHub. Go to the
GitHub page for the tutorial repository located here:
<https://github.com/cornell-ece2400/ece2400-tut2-c-basics>. Click on
_Fork_ in the upper right-hand corner. If asked where to fork this
repository, choose your personal GitHub account. After a few seconds, you
should have a new repository in your account:
`https://github.com/githubid/ece2400-tut2-c-basics` Where `githubid` is
your GitHub username on [github.com](). Now access an `ecelinux` machine
and clone your copy of the tutorial repository as follows:

    :::bash
    % source setup-ece2400.sh
    % mkdir -p ${HOME}/ece2400
    % cd ${HOME}/ece2400
    % git clone https://github.com/githubid/ece2400-tut3-c-basics.git tut3
    % cd tut3
    % TUTROOT=${PWD}

!!! note
    It should be possible to experiment with this tutorial even if you
    are not enrolled in the course and/or do not have access to the
    course computing resources. All of the code for the tutorial is
    located on GitHub. You will not use the `setup-ece2400.sh` script,
    and your specific environment may be different from what is assumed
    in this tutorial.

1. Using the C Preprocessor
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
learn at least the basics. You can find out more about the C preprocessor
here:

 - <http://en.cppreference.com/w/c/preprocessor>
 - <https://en.wikibooks.org/wiki/C_Programming/Preprocessor>

### 1.1. The `#define` Directive

The best way to understand the C preprocessor is actually to use it to
preprocess standard text files as opposed to C source files. Assume we
are writing a report on the history of Cornell University, and we
have a common snippet of text that we use often in our text file.

 - text with many of same code
 - define that
 - mention ALL_CAPS
 - define with argument

try to avoid this -- will see it in our test macros

### 1.2. The `#ifdef` Directive

### 1.3. The `#include` Directive

The best way to understand the C preprocessor is actually to use it to
preprocess standard text files as opposed to C source files. Assume we
wish to create a text file which lists pioneering women and men in the
field of computer science. We might start with a text file of pioneering
women:

    :::text
    ==========================================================================
    Pioneers in Computer Science
    ==========================================================================
    Computer science is a relatively recent field which focuses on the
    theory, experimentation, and engineering that form the basis for the
    design and use of computers. Here is a very small subset of the many
    individuals who helped in the creation, development, and imagining of
    what computers and electronics could do.

     - Eva Tardos        : fundamental contributions to algorithm analysis
     - Mary Jane Irwin   : early work on design automation and computer arch
     - Barbara Liskov    : fundamental contributions to obj-oriented progr
     - Frances Allen     : pioneer in optimizing compilers
     - Grace Hopper      : pioneer in computer prog and high-level languages
     - Jean Bartik       : one of the first computer programmers
     - Ada Lovelace      : began the study of scientific computation

and a separate text file of pioneering men:

    :::text
    ==========================================================================
    Pioneers in Computer Science
    ==========================================================================
    Computer science is a relatively recent field which focuses on the
    theory, experimentation, and engineering that form the basis for the
    design and use of computers. Here is a very small subset of the many
    individuals who helped in the creation, development, and imagining of
    what computers and electronics could do.

     - Donald Knuth      : fundamental contributions to algorithm analysis
     - John Mauchly      : designed and built first modern computer
     - J. Presper Eckert : designed and built first modern computer
     - John Von Neumann  : formulated the von Neumann architecture
     - Maurice Wilkes    : built first practical stored program computer
     - Alan Turing       : invented Turning model, stored program concept
     - Charles Babbage   : originated concept of programmable computer

These two files require some duplication, since both files include a
short introductory paragraph. To avoid this redundancy, we can first
refactor this introductory paragraph into its own dedicated text file,
and we can then use the C processor to _include_ this file at the
beginning of each list of pioneers. This new approach is illustrated
below. First, we have a file named `cs-pioneers-intro.txt` with the
introductory paragraph.

    :::text
    ==========================================================================
    Pioneers in Computer Science
    ==========================================================================
    Computer science is a relatively recent field which focuses on the
    theory, experimentation, and engineering that form the basis for the
    design and use of computers. Here is a very small subset of the many
    individuals who helped in the creation, development, and imagining of
    what computers and electronics could do.

Then, we have a file named `cs-pioneers-women-in.txt` containing a list
of pioneering women in computer science:

    :::text
    #include "cs-pioneers-intro.txt"

     - Eva Tardos        : fundamental contributions to algorithm analysis
     - Mary Jane Irwin   : early work on design automation and computer arch
     - Barbara Liskov    : fundamental contributions to obj-oriented progr
     - Frances Allen     : pioneer in optimizing compilers
     - Grace Hopper      : pioneer in computer prog and high-level languages
     - Jean Bartik       : one of the first computer programmers
     - Ada Lovelace      : began the study of scientific computation

Finally, we have a file named `cs-pioneers-men-in.txt` containing a list
of pioneering men in computer science:

    :::text
    #include "cs-pioneers-intro.txt"

     - Donald Knuth      : fundamental contributions to algorithm analysis
     - John Mauchly      : designed and built first modern computer
     - J. Presper Eckert : designed and built first modern computer
     - John Von Neumann  : formulated the von Neumann architecture
     - Maurice Wilkes    : built first practical stored program computer
     - Alan Turing       : invented Turning model, stored program concept
     - Charles Babbage   : originated concept of programmable computer

We can then use the C preprocessor to _preprocess_ these files. The C
preprocessor copies the input source file to the output source file,
while also looking for _C preprocessor directives_. All C preprocessor
directives begin with the special `#` character. Line 1 in
`cs-pioneers-women-in.txt` and `cs-pioneers-men-in.txt` uses the
`#include` directive which specifies the file name of a different text
file to include. The file name should be be specified using double quotes
(`""`).

You should have already cloned the tutorial repository and set the
`TUTROOT` environment variable at the beginning of this tutorial. Let's
make a new directory to work in for this section:

    :::bash
    % mkdir ${TUTROOT}/tut3-cpp
    % cd ${TUTROOT}/tut3-cpp

Now create the three text files mentioned above: `cs-pioneers-intro.txt`,
`cs-pioneers-women-in.txt`, and `cs-pioneers-men-in.txt` using Geany or
the text editor of your choice. See the first tutorial for more on using
Geany or other text editors. The three files should be in the `tut3-cpp`
subdirectory. All text files have a `.txt` filename extension. In
general, we prefer using dashes (`-`) instead of underscores to separate
words in file names.

Let's use the C preprocessor (`cpp`) to preprocess the `-in.txt` files
into two final text files that contain both the introductory paragraph
and the list of pionners in computer science.

    :::bash
    % cd ${TUTROOT}/tut3-cpp
    % cpp -o cs-pioneers-women.txt cs-pioneers-women-in.txt
    % cat cs-pioneers-women.txt
    % cpp -o cs-pioneers-men.txt cs-pioneers-men-in.txt
    % cat cs-pioneers-men.txt

The `-o` command line option is used to specify the name of the output
file. The outpfile `cs-pioneers-women.txt` should look like this:

    :::text
    # 1 "cs-pioneers-women-in.txt"
    # 1 "<built-in>"
    # 1 "<command-line>"
    # 1 "cs-pioneers-women-in.txt"
    # 1 "cs-pioneers-intro.txt" 1


    ==========================================================================
    Pioneers in Computer Science
    ==========================================================================
    Computer science is a relatively recent field which focuses on the
    theory, experimentation, and engineering that form the basis for the
    design and use of computers. Here is a very small subset of the many
    individuals who helped in the creation, development, and imagining of
    what computers and electronics could do.
    # 2 "cs-pioneers-women-in.txt" 2
     - Eva Tardos : fundamental contributions to algorithm analysis
     - Mary Jane Irwin : early work on design automation and computer arch
     - Barbara Liskov : fundamental contributions to obj-oriented progr
     - Frances Allen : pioneer in optimizing compilers
     - Grace Hopper : pioneer in computer prog and high-level languages
     - Jean Bartik : one of the first computer programmers
     - Ada Lovelace : began the study of scientific computation

The C preprocessor included the introductory paragraph correctly, but it
has also included some additional lines beginning with the `#` character
to specify information about where all of the pieces of text originally
came from. For example, Line 5 indicates that the introductory paragraph
came from the `cs-pioneers-intro.txt` file. We can tell the `cpp` to not
include this extra metadata with the `-P` command line option.

    :::bash
    % cd ${TUTROOT}/tut3-cpp
    % cpp -P -o cs-pioneers-women.txt cs-pioneers-women-in.txt
    % cat cs-pioneers-women.txt
    % cpp -P -o cs-pioneers-men.txt cs-pioneers-men-in.txt
    % cat cs-pioneers-men.txt

This example illustrates the first way we will use the C preprocessor. We
will use the `#include` directive to include common C source files in
several of our own C source files. This approach avoids redundancy and
makes our programs much easier to maintain since we can make changes in a
single C source file, and those changes can be immediately reflected in
any program which includes that C source file. We have actually already
seen this use of the C preprocessor in the previous section when we
included the \TT{stdio.h} header file which includes the declaration of
the \TT{printf} function (see Line~1 in
Figure~\ref{fig-tut3-code-avg-main}). If the file in an \verb|#include|
directive is specified using angle brackets (\TT{<>}) then this tells the
C preprocessor that the file to include is part of the system and is
installed in a default location. If the given file is specified using
double quotes (\TT{""}), then this tells the C preprocessor that the file
to be included is part of the user program. The C preprocessor does not
automatically know where to find extra user files, so we might need to
add extra command line options to \TT{cpp} to tell the C preprocessor
where to search for files.

### 1.4. Include Guards

2. Writing a Single-File C Program
--------------------------------------------------------------------------

3. Compiling a Single-File C Program
--------------------------------------------------------------------------

4. Writing a Multi-File C Program
--------------------------------------------------------------------------

5. Compiling a Multi-File C Program
--------------------------------------------------------------------------

