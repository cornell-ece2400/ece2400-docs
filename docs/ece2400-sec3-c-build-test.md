
Section 3: C Build and Test Frameworks
==========================================================================

In the previous discussion section, you learned how to explicitly compile
and run C programs from the command line. You learned how to use the GNU
C Compiler (`gcc`) to compile both a single-file and multi-file program
that calculated the average of two integers. You probably noticed that it
can be tedious to have to carefully enter the correct commands on the
command line. We also need to carefully track which steps need to be
redone whenever we change a C source file. In this discussion section, we
will explore using a _build framework_ based on CMake to automate this
process. In the previous discussion section, you also learned how to do
ad-hoc testing by executing a function and then simply printing out the
result to the terminal. In this discussion section, we will explore using
a _test framework_ to automate this process. Using a build and test
framework is critical to productive system-level programming in C and
C++.

Follow the same process as in the previous discussion section. You need
to login to a workstation with your NetID and password. Start a terminal
and then don't forget to source the setup script!

    :::bash
    % source setup-ece2400.sh

Now clone the github repo for this discussion section.

    :::bash
    % mkdir -p ${HOME}/ece2400
    % cd ${HOME}/ece2400
    % git clone git@github.com:cornell-ece2400/ece2400-sec3-c-build-test sec3
    % cd sec3/src
    % ls

The given `src` directory includes the following files:

 - `avg-sfile.c`: source and main for single-file `avg` program
 - `avg.h`: header file for the `avg` function
 - `avg.c`: source file for the `avg` function
 - `avg-mfile.c` : `main` for multi-file `avg` program
 - `avg-mfile-basic-tests.h` : most basic smoke test
 - `utst.h` : simple C preprocessor macros for unit testing

1. Basic Makefile for Compiling C Programs
--------------------------------------------------------------------------

Let's remind ourselves how to explicitly compile and run a single-file C
program on the command line:

    :::bash
    % cd ${HOME}/ece2400/sec3/src
    % gcc -Wall -o avg-sfile avg-sfile.c
    % ./avg-sfile

Let's now remove the binary so we are back to a clean directory:

    :::bash
    % cd ${HOME}/ece2400/sec3/src
    % rm -rf avg-sfile

We will start by using a new tool called `make` which was specifically
designed to help automate the process of building C programs. The key to
using `make` is developing a `Makefile`. A `Makefile` is a plain text
file which contains a list of _rules_ which together specify how to
execute commands to accomplish some task. Each rule has the following
syntax:

    target : prerequisite0 prerequisite1 prerequisite2
    <TAB>command

A rule specifies how to generate the target file using the list of
prerequisite files and the given Linux command. `make` is smart enough to
know it should rerun the command if any of the prerequisites change, and
it also knows that if one of the prerequisites does not exist then it
needs to look for some other rule to generate that prerequisite first. It
is very important to note that `make` requires commands in a rule to
start with a real TAB character. So you should not type the letters
`<TAB>`, but you should instead press the TAB key and verify that it has
inserted a real TAB character (i.e., if you move the left/right arrows
the cursor should jump back and forth across the TAB). This is the only
time in the course where you should use a real TAB character as opposed
to spaces.

Let's create a simple `Makefile` to compile a single-file C program. Use
your favorite text editor to create a file named `Makefile` in the `src`
directory with the following content:

    :::makefile
    avg-sfile: avg-sfile.c
    <TAB>gcc -Wall -o avg-sfile avg-sfile.c

    clean:
    <TAB>rm -rf avg-sfile

We can use the newly created `Makefile` like this:

    :::bash
    % cd ${HOME}/ece2400/sec3/src
    % make avg-sfile
    % ./avg-sfile

`make` will by default use the `Makefile` in the current directory.
`make` takes a command line argument specifying what you want "make". In
this case, we want to make the `avg-sfile` executable. `make` will look
at all of the rules in the `Makefile` to find a rule that specifies how
to make the `avg-sfile` executable. It will then check to make sure the
prerequisites exist and that they are up-to-date, and then it will run
the command specified in the rule for `avg-sfile`. In this case, that
command is `gcc`. `make` will output to the terminal every command it
runs, so you should see it output the command line which uses `gcc` to
generate the `avg-sfile` executable.

Try running `make` again:

    :::bash
    % cd ${HOME}/ece2400/sec3/src
    % make avg-sfile
    % ./avg-sfile

`make` detects that the prerequisite (i.e., `avg-sfile.c`) has not
changed and so it does not recompile the executable. Now let's try making
a change in the `avg-sfile.c` source file. Modify the `printf` statement
as follows:

    :::c
    printf("avg( %d, %d ) == %d\n", a, b, c );

You can recompile and re-execute the program like this:

    :::bash
    % cd ${HOME}/ece2400/sec3/src
    % make avg-sfile
    % ./avg-sfile

`make` will automatically detect that the prerequisite has changed and
recompile the executable appropriately. This ability to automatically
track dependencies and recompile just what is necessary is a key benefit
of using a tool like `make`. `Makefiles` can also include targets which
are not actually files. Our example `Makefile` includes a `clean` target
which will delete any generated executables. Let's clean up our directory
like this:

    :::bash
    % cd ${HOME}/ece2400/sec3/src
    % ls
    % make clean
    % ls

!!! question "To-Do On Your Own"

    Add two rules to your `Makefile` to compile `avg.o` and
    `avg-mfile.o`. Add a rule that links these two object files together
    and produces `avg-mfile`. Update the rule for the `clean` target
    appropriately. Carefully consider what command and prerequisites to
    use for each target. Test out your `Makefile`. Try changing `avg.c`
    and rerunning `make`. Does your program recompile correctly? Try
    changing `avg.h` and rerunning `make`. Does your program recompile
    correctly?

2. Using CMake to Generate Makefiles for Compiling C Programs
--------------------------------------------------------------------------

While using `make` can help automate the build process, the corresponding
`Makefiles` can quickly grow to be incredibly complicated. Creating and
maintaining these `Makefiles` can involve significant effort. It can be
particularly challenging to ensure all of the dependencies between the
various source and header files are always correctly captured in the
`Makefile`. It can also be complicated to add support for code coverage,
memory checking, and debug vs.~evaluation builds.

New tools have been developed to help _automate_ the process of managing
`Makefiles` (which in turn _automate_ the build process). Automation is
the key to effective software development methodologies. In this course,
we will be using CMake as a key step in our build framework. CMake takes
as input a simple `CMakeLists.txt` file and _generates_ a sophisticated
`Makefile` for us to use. A `CMakeLists.txt` is a plain text file with a
list of commands that specify what tasks we would like the generated
`Makefile` to perform.

Before getting started let's remove any files we have generated and also
remove the `Makefile` we developed in the previous section.

    :::bash
    % cd ${HOME}/ece2400/sec3/src
    % make clean
    % trash Makefile

Let's create a simple `CMakeLists.txt` that can be used to generate a
`Makefile` which will in turn be used to compile a single-file C program.
User your favorite text editor to create a file named `CMakeLists.txt` in
the `src` directory with the following content:

    :::cmake
    cmake_minimum_required(VERSION 2.8)
    enable_language(C)
    add_executable( avg-sfile avg-sfile.c )

Line 1 specifies the CMake version we are assuming, and line 2 specifies
that we will be using CMake with a C project. Line 3 specifies that we
want to generate a `Makefile` that can compile an executable named
`avg-sfile` form the `avg-sfile.c` source file. Now let's run the `cmake`
command to generate a `Makefile` we can use to compile `avg-sfile`:

    :::bash
    % cd ${HOME}/ece2400/sec3/src
    % cmake .
    % ls
    % less Makefile

The `cmake` command will by default use the `CMakeLists.txt` in the
directory given as a command line argument. CMake takes care of figuring
out what C compilers are available and then generating the `Makefile`
appropriately. You can see that CMake has automatically generated a
pretty sophisticated `Makefile`. Let's go ahead and use this `Makefile`
to build `avg-sfile`.

    :::bash
    % cd ${HOME}/ece2400/sec3/src
    % make avg-sfile
    % ./avg-sfile

CMake will automatically create some useful targets like `clean`.

    :::bash
    % cd ${HOME}/ece2400/sec3/src
    % make clean

Writing a `CMakeLists.txt` is simpler than writing a `Makefile`,
especially when we start working with many files.

!!! question "To-Do On Your Own"

    Add another line to your `CMakeLists.txt` file to specify that we
    want to generate a `Makefile` that can be used to compile `avg-mfile`
    from `avg-mfile.c` and `avg.c`. Use CMake to generate the
    corresponding `Makefile` and then use `make` to compile `avg-mfile`.
    Try changing `avg.c` and rerunning `make`. Does your program
    recompile correctly? Try changing `avg.h` and rerunning `make`. Does
    your program recompile correctly?

3. Using CTest for Systematic Unit Testing
--------------------------------------------------------------------------

So far we have been using "ad-hoc testing". For example, the `main`
function in `avg-sfile.c` will execute the `avg` function with one set of
inputs and then print the result to the terminal. If it is not what we
expected, we can debug our program until it meets our expectations.
Unfortunately, ad-hoc testing is error prone and not easily reproducible.
If you later make a change to your implementation, you would have to take
another look at the output to ensure your implementation still works. If
another developer wants to understand your implementation and verify that
it is working, he or she would also need to take a look at the output and
think hard about what is the expected result. Ad-hoc testing is usually
verbose, which makes it error prone, and does not use any kind of
standard test output. While ad-hoc testing might be feasible for very
simple implementations, it is obviously not a scalable approach when
developing the more complicated implementations we will tackle in this
course.

New tools have been developed to help automate the process of testing
implementations. These tools provide a _systematic_ way to do automated
unit testing including standardized naming conventions, test output, and
test drivers. In this course, we will be using CTest as a key step in our
test framework. CTest elegantly integrates with CMake to create a unified
built and test framework. Each unit test will be a stand-alone test
program where the test code is contained within the `main` function. The
following is an example of a unit test program for our `avg` function:

    :::c
    #include <stdio.h>
    #include "avg.h"
    #include "utst.h"

    int main()
    {
      UTST_BEGIN();

      UTST_ASSERT_INT_EQ( avg( 10, 20 ), 15 );

      UTST_END();
      return 0;
    }

We provide a simple library of test macros in `utst.h` which can be used
to write various testing assertions. You should always insert
`UTST_BEGIN()` at the beginning of `main` and `UTST_END()` at the end of
main immediately before the final `return` statement. The
`UTST_ASSERT_INT_EQ` macro asserts that the two given integer parameters
are equal. If they are indeed equal, then the macro prints out the
values, and we move on to the next test assertion. If they are not equal,
the the macro prints out an error message and returns from `main` with
the value 1. Recall that when `main` returns 0 it means success, and when
`main` returns 1 it means failure. The return value enables our test
program to inform CTest of whether or not our test passed of failed.

To use CTest, we need to tell it about this new test. We can do this by
simply adding a new line to our `CMakeLists.txt` file. Here is an example
`CMakeLists.txt` file:

    :::cmake
    cmake_minimum_required(VERSION 2.8)
    enable_language(C)
    enable_testing()

    add_executable( avg-sfile avg-sfile.c )
    add_executable( avg-mfile avg-mfile.c avg.c )

    add_executable( avg-mfile-basic-tests avg-mfile-basic-tests.c avg.c )
    add_test( avg-mfile-basic-tests avg-mfile-basic-tests )

Line 3 tells CMake to turn on support for testing with CTest. Line 6
specifies how to build `avg-mfile`. Line 8 specifies how to build the
`avg-mfile-basic-tests` test program. Line 9 tells CMake that
`avg-mfile-basic-tests` is a test that should be managed by CTest. Modify
your `CMakeLists.txt` file to look like what is given above, rerun cmake,
build the test, and run it.

    :::bash
    % cd ${HOME}/ece2400/sec3/src
    % cmake .
    % make avg-mfile-basic-tests
    % ./avg-mfile-basic-tests

You should see some output which indicates the passing test assertion.
CMake provides a `test` target which can run all of the tests and
provides a summary.

    :::bash
    % cd ${HOME}/ece2400/sec3/src
    % make test

It is always a good idea to occasionally force a test to fail to ensure
your test framework is behaving correctly. Change the test assertion in
`avg-mfile-basic-tests.c` to look like this:

    :::c
    UTST_ASSERT_INT_EQ( avg( 10, 20 ), 16 );

Then rebuild and rerun the test like this:

    :::bash
    % cd ${HOME}/ece2400/sec3/src
    % make avg-mfile-basic-tests
    % make test
    % ./avg-mfile-basic-tests

You should see the test failing in the test summary, and then see
additional information about the failing test assertion when you
explicitly run the test program. `avg-mfile-basic-tests` is a kind of
"smoke" test which is used to test the absolute most basic functionality
of an implementation. We will also be doing extensive _directed testing_
and _random testing_. In directed testing, you explicitly use test
assertions to test as many corner cases as possible. In random testing,
you use random input values and compare the output to some golden
"reference" implementation to hopefully catch bugs missed in your
directed testing.

!!! question "To-Do On Your Own"

    Create another unit test program named `avg-mfile-directed-tests.c`
    for directed testing. Use the macros in `utst.h` to begin/end your
    test program and for test assertions. Try to test several different
    corner cases. Modify your `CMakeLists.txt` file to include this new
    unit test program. Use CMake to regenerate the corresponding
    `Makefile`, use `make` to build your test program, and then run it.
    Ensure that `make test` runs both the basic and directed tests.

4. Using a Build Directory
--------------------------------------------------------------------------

Take a look at the source directory. It likely contains a mess of
generated directories, object files, executables, etc. It is usually very
bad practice to build C programs directly in the _source_ directory. It
is much better to build C programs in a completely separate _build_
directory. Adding support for these build directories in a `Makefile` is
complex, but CMake makes it easy. Let's start by deleting all generated
content in your source directory:

    :::bash
    % cd ${HOME}/ece2400/sec3/src
    % make clean
    % trash CMakeCache.txt CMakeFiles *.cmake

Now let's first create a separate build directory, use CMake to create
a new `Makefile`, and finally build and run all of our tests.

    :::bash
    % cd ${HOME}/ece2400/sec3
    % mkdir build
    % cd build
    % cmake ../src
    % make
    % make test

A separate build directory makes it easy to do a "clean build" where you
start your build from scratch. Simply remove the build directory and
start again like this:

    :::bash
    % cd ${HOME}/ece2400/sec3
    % trash build
    % mkdir build
    % cd build
    % cmake ../src
    % make
    % make test

You should **never** check in your `build` directory or any generated
content into Git. Only source files are checked into Git!

!!! question "To-Do On Your Own"

    Add a new test assertion to your directed tests. Rebuild and rerun
    the test program in the separate build directory.

5. Try Steps for Programming Assignments
--------------------------------------------------------------------------

For each programming assignment, we will provide you a skeleton for your
project including a complete `CMakeLists.txt`. In the common case, you
should not need to modify the `CMakeLists.txt` unless you want to
incorporate additional source and/or test files. The programming
assignments are setup to use a separate build directory. The programming
assignments also group all of the tests into their own separate
directory. You can use the following steps to clone and build the first
programming assignment.

    :::bash
    % mkdir -p ${HOME}/ece2400
    % cd ece2400
    % git clone git@github.com:cornell-ece2400/netid
    % cd ece2400-pa-release/pa1-math
    % tree
    % mkdir build
    % cd build
    % cmake ..
    % make
    % make test

Where `netid` is your NetID. We provide you convenient `check` targets
which should be the primary way you build and run your tests. These
targets take care of making sure your test programs are always up-to-date
before running them. The following will run all of the tests for the
first programming assignment:

    :::bash
    % cd ${HOME}/ece2400/netid/pa1-math/build
    % make check

If there is a test failure, we can "zoom in" and run just the tests for
the corresponding implementation like this:

    :::bash
    % cd ${HOME}/ece2400/netid/pa1-math/build
    % make check-pow-iter

Then we can "zoom in" further, and run a single test program so we can
see exactly which test assertion is failing. We should always start by
debugging the simplest test program first (i.e., basic tests) before
moving on to directed or random tests.

    :::bash
    % cd ${HOME}/ece2400/netid/pa1-math/build
    % make check-pow-iter-basic-tests

Once we fix the bug, then we can "zoom out" and move on to the next
failing test program, or to the next implementation.

!!! question "To-Do On Your Own"

    Force one of your directed test assertions to fail. Use the `check`
    targets to "zoom in", fix the bug, and then "zoom out".

