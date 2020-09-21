
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

1. Logging Into ecelinux
--------------------------------------------------------------------------

Follow the same process as in the last section. If you are participating
in the discussion section in-person in 225 Upson Hall, then all you need
to do is find a free workstation and log into the workstation using your
NetID and standard NetID password. Remote access students might also need
to start the Cornell VPN. Then connect to `ecelinux` using X2go:

 - start X2go (in Upson 225 just double click the X2Go client on the desktop)
 - double click on the `ecelinux` session or ...
 - setup a new X2go session by configuring the _Session_ and _Media_ tabs
    - session name: _ecelinux_
    - host: _ecelinux.ece.cornell.edu_
    - login: _cb535_
    - session type: _XFCE_
    - uncheck _enable sound support_
    - uncheck _client side printing support_
 - click _OK_
 - enter your standard NetID password
 - if asked to trust a certificate for the servers, click _yes_
 - if asked about a Windows firewall setup, click _cancel_
 - open a terminal using _Applications > Terminal Emulator_
    from the _Applications_ menu
 - make sure you source the setup script
 - verify ECE2400 is in your prompt

Now clone the GitHub repo we will be using in this section using the
following commands:

    :::bash
    % source setup-ece2400.sh
    % mkdir -p ${HOME}/ece2400
    % cd ${HOME}/ece2400
    % git clone git@github.com:cornell-ece2400/ece2400-sec3 sec3
    % cd sec3
    % cat README.md

The given `src` directory includes the following files:

 - `ece2400-stdlib.h` : ECE 2400 course standard library header
 - `ece2400-stdlib.c` : ECE 2400 course standard library implementation
 - `avg-sfile.c`: source and main for single-file `avg` program
 - `avg.h`: header file for the `avg` function
 - `avg.c`: source file for the `avg` function
 - `avg-mfile.c` : `main` for multi-file `avg` program
 - `avg-mfile-basic-test.c` : most basic smoke test

2. Using Makefiles to Compile C Programs
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

!!! note "To-Do On Your Own"

    Add two rules to your `Makefile` to compile `avg.o` and
    `avg-mfile.o`. Add a rule that links these two object files together
    and produces `avg-mfile`. Update the rule for the `clean` target
    appropriately. Carefully consider what command and prerequisites to
    use for each target. Test out your `Makefile`. Try changing `avg.c`
    and rerunning `make`. Does your program recompile correctly? Try
    changing `avg.h` and rerunning `make`. Does your program recompile
    correctly?

3. Using CMake to Generate Makefiles for Compiling C Programs
--------------------------------------------------------------------------

While using `make` can help automate the build process, the corresponding
`Makefiles` can quickly grow to be incredibly complicated. Creating and
maintaining these `Makefiles` can involve significant effort. It can be
particularly challenging to ensure all of the dependencies between the
various source and header files are always correctly captured in the
`Makefile`. It can also be complicated to add support for code coverage,
memory checking, and debug vs. evaluation builds.

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

!!! note "To-Do On Your Own"

    Add another line to your `CMakeLists.txt` file to specify that we
    want to generate a `Makefile` that can be used to compile `avg-mfile`
    from `avg-mfile.c` and `avg.c`. Use CMake to generate the
    corresponding `Makefile` and then use `make` to compile `avg-mfile`.
    Try changing `avg.c` and rerunning `make`. Does your program
    recompile correctly? Try changing `avg.h` and rerunning `make`. Does
    your program recompile correctly?

4. Using CTest for Systematic Unit Testing
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
program. The following is an example of a unit test program for our `avg`
function:

    :::c
    #include "avg.h"
    #include "ece2400-stdlib.h"
    #include <stdio.h>

    void test_case_1_basic()
    {
      printf("\n%s\n", __func__  );
      ECE2400_CHECK_INT_EQ( avg( 10, 20 ), 15 );
    }

    int main( int argc, char* argv[] )
    {
      __n = ( argc == 1 ) ? 0 : atoi( argv[1] );

      if ( (__n <= 0) || (__n == 1) )
        test_case_1_basic();

      printf( "\n" );
      return __failed;
    }

Our test programs will consist of a number of _test cases_. Each test
case is a separate function which should focus on testing a specific
subset of inputs. In this example, test case 1 tests very basic
functionality. Each test case should start with a statement similar to
line 7. `__func__` is a built-in variable which contains the function
name, so these lines basically print out the name of the function. Each
test case should then use a series of `ECE2400_CHECK` macros to check
that the implementation produces the expected results. For example, on
line 8 we check that the average of 10 and 20 is 15. If the check passes,
then the macro prints out the values, and we move on to the next test
check. If the check fails, then the macro prints out an error message,
sets the global `__failed` variable, and returns ending that test case.
The return value enables our test program to inform CTest of whether or
not all of our test cases passed of failed. The main function's job is to
simply call each test case function. Note that we get a single command
line argument which specifies which test case we want to run. If we do
not specify a command line argument then we run all of the test cases.

We have provided the above test program in the repository for this
discussion section. To use CTest, we need to tell it about this new test
program. We can do this by simply adding a new line to our
`CMakeLists.txt` file. Here is an example `CMakeLists.txt` file:

    :::cmake
    cmake_minimum_required(VERSION 2.8)
    enable_language(C)
    enable_testing()

    add_executable( avg-sfile avg-sfile.c )
    add_executable( avg-mfile avg-mfile.c avg.c )

    add_executable( avg-mfile-directed-test avg-mfile-directed-test.c avg.c )
    add_test( avg-mfile-directed-test avg-mfile-directed-test )

Line 3 tells CMake to turn on support for testing with CTest. Line 6
specifies how to build `avg-mfile`. Line 8 specifies how to build the
`avg-mfile-directed-test` test program. Line 9 tells CMake that
`avg-mfile-directed-test` is a test that should be managed by CTest. Modify
your `CMakeLists.txt` file to look like what is given above, rerun cmake,
build the test, and run it.

    :::bash
    % cd ${HOME}/ece2400/sec3/src
    % cmake .
    % make avg-mfile-directed-test
    % ./avg-mfile-directed-test
    % ./avg-mfile-directed-test 1

The command `./avg-mfile-directed-test` runs all test cases defined in
`avg-mfile-directed-test` and does not print anything when the test
case passes. If you want to see more information about the passing
assertions, you can zoom in to a specific test case (e.g., test case 1)
using `./avg-mfile-directed-test 1`. `avg-mfile-directed-test` is a kind
of "smoke" test which is used to test the absolute most directed
functionality of an implementation. We will also be doing extensive
_directed testing_ and _random testing_. In directed testing, you explicitly
use test assertions to test as many corner cases as possible. In random
testing, you use random input values and compare the output to some
golden "reference" implementation to hopefully catch bugs missed in your
directed testing.

CMake provides a `test` target which can run all of the tests and
provides a summary.

    :::bash
    % cd ${HOME}/ece2400/sec3/src
    % make test

It is always a good idea to occasionally force a test to fail to ensure
your test framework is behaving correctly. Use your favoriate text
editor and change file `avg.c` to look like this:

    :::c
    #include "avg.h"

    int avg( int x, int y )
    {
      int sum = x + x;
      return sum / 2;
    }

Note how `sum` is calculated as the sum of `x` and `x` instead of `x` and
`y`. Then rebuild and rerun the test like this:

    :::bash
    % cd ${HOME}/ece2400/sec3/src
    % make avg-mfile-directed-test
    % make test
    % ./avg-mfile-directed-test

You should see the test failing in the test summary, and then see
additional information about the failing test assertion when you
explicitly run the test program.

The ECE 2400 course standard library provides `ECE2400_DEBUG`, a macro that
allows you to print out extra information for debugging purposes. One
advantage of using the debug macro over `printf` is that this macro incurs
zero run time overhead in the evaluation build. Indeed, this macro is simply
replaced with a semi-colon when the build type is specified as `eval`. That
means the same implementation of your `sqrt` or `pow` function can be reused
for evaluation purposes without removing all debugging `printf`s first. To
use the debug macro, change file `avg.c` to look like this:

    :::c
    #include "avg.h"
    #include "ece2400-stdlib.h"

    int avg( int x, int y )
    {
      int sum = x + x;
      ECE2400_DEBUG("sum is %d", sum);
      return sum / 2;
    }

Next, change `CMakeLists.txt` to look like this:

    :::cmake
    cmake_minimum_required(VERSION 2.8)
    enable_language(C)
    enable_testing()

    add_executable( avg-sfile avg-sfile.c )
    add_executable( avg-mfile avg-mfile.c avg.c ece2400-stdlib.c )

    add_executable( avg-mfile-directed-test avg-mfile-directed-test.c avg.c ece2400-stdlib.c )
    add_test( avg-mfile-directed-test avg-mfile-directed-test )

Since we are using utilities from the course standard library, we need
to compile targets who depend on `avg.c` together with the implementation
of the course library (line 6 and 8). Then rebuild and rerun the test like this:

    :::bash
    % cd ${HOME}/ece2400/sec3/src
    % make avg-mfile-directed-test
    % ./avg-mfile-directed-test
    % ./avg-mfile-directed-test 1

Note how the debug info is suppressed when you are not zooming into a
specific test case. You should see a line starting with `[ -info- ]` in
the output, which corresponds to the debug macro we added in `avg.c`. This
info line tells us the sum of `x` and `y` is 20 instead of 30, and we
should realize that we made a mistake while calculating the sum.

!!! note "To-Do On Your Own"

    In this to-do activity you will work on `avg-mfile-directed-test` to
    add one more test case. Add a second test case named `test_case_2_truncate`
    which explicitly tests situations where the average has to be truncated
    (e.g., the average of 10 and 15 is 12.5 which will be truncated to 12).
    Update the `main` function to call this new test case. Use CMake and
    CTest to rerun the tests.

5. Using a Build Directory
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
    % trash CMakeCache.txt CMakeFiles *.cmake Testing

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

6. Experimenting with Build and Test Frameworks for PA1
--------------------------------------------------------------------------

Let's experiment with the build and test frameworks for the first
programming assignment using what we have learned in this discussion
section. You can use the following steps to clone your PA1 repo.

    :::bash
    % mkdir -p ${HOME}/ece2400
    % cd ${HOME}/ece2400
    % git clone git@github.com:cornell-ece2400/netid
    % cd netid
    % tree

For each programming assignment, we will provide you a skeleton for your
project including a complete `CMakeLists.txt`. In the common case, you
should not need to modify the `CMakeLists.txt` unless you want to
incorporate additional source and/or test files. The programming
assignments are setup to use a separate build directory. The programming
assignments also group all of the tests into their own separate
directory. You can use the following steps to use the build framework
with the first programming assignment.

    :::bash
    % cd ${HOME}/ece2400/netid/pa1-math
    % mkdir build
    % cd build
    % cmake ..
    % make check
    % make check-milestone

`check` will run all of the tests for the entire PA, while
`check-milestone` will only run the tests for the milestone. If there is
a test failure, we can "zoom in" to build a single test program and run
it in isolation like this:

    :::bash
    % cd ${HOME}/ece2400/netid/pa1-math/build
    % make sqrt-iter-directed-test
    % ./sqrt-iter-directed-test

You can build and run the test program on a single line like this:

    :::bash
    % cd ${HOME}/ece2400/netid/pa1-math/build
    % make sqrt-iter-directed-test && ./sqrt-iter-directed-test

The `&&` bash operator enables running multiple commands on the same
command line. Without a command line argument, the test program will run
all of the test cases. You can also use the command line argument `-1` to
print out a dot for every passing test check. Then we can "zoom in"
further, and run a single test case within a single test program so we
see exactly which test check is failing. The following will build the
directed test program, explicitly run just test case 1, and then
explicitly run just test case 2.

    :::bash
    % cd ${HOME}/ece2400/netid/pa1-math/build
    % make sqrt-iter-directed-test
    % ./sqrt-iter-directed-test 1
    % ./sqrt-iter-directed-test 2

Once we fix the bug, then we can "zoom out" and move on to the next
failing test case, or to the next failing test program.

