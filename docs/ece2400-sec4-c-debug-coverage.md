
Section 4: C Debug and Code Coverage
==========================================================================

In the previous discussion section, you learned how to use C build and
test frameworks to help automate the process of compiling and verifying
your programs In this discussion, we will continue to learn about new
tools that can help us better debug and verify our programs.

Follow the same process as in the previous discussion section. You need
to login to a workstation with your NetID and password. Start a terminal
and then don't forget to source the setup script!

    :::bash
    % source setup-ece2400.sh

Now fork the github repo for this discussion section so you have your own
personal copy. Go to the example repo here:

 - https://github.com/cornell-ece2400/ece2400-sec4-c-debug-coverage

Click on the "Fork" button. Wait a few seconds and then visit the new
copy of this repo in your own person GitHub workspace:

 - `https://github.com/githubid/ece2400-sec2`

Where `githubid` is your GitHubID. Now clone the github repo for this
discussion section.

    :::bash
    % mkdir -p ${HOME}/ece2400
    % cd ${HOME}/ece2400
    % git clone git@github.com:githubid/ece2400-sec4-c-debug-coverage sec4
    % cd sec4
    % ls

Where again, the `githubid` is your GitHubID. The given `src` directory
includes the following files:

 - `avg-test.c`: source and test for `avg` function
 - `gcd-test.c`: source and test for `gcd` function
 - `utst.h` : simple C preprocessor macros for unit testing
 - `.travis.yml` : TravisCI configuration script

1. Using GDB for Debugging
--------------------------------------------------------------------------

There are two kinds of C/C++ programmers in the world: printf debuggers
and GDB debuggers. Prof. Batten used to be a printf debuggers but
teaching this course has converted him to be a GDB advocate. He will
share his perspectives on this in the discussion section.

Let's start by compiling a single-file program that uses the `UTST` macros
to test our ubiquitous `avg` function.

    :::bash
    % cd ${HOME}/ece2400/sec4
    % gcc -Wall -g -o avg-test avg-test.c
    % ./avg-test

Notice how we include the `-g` option to turn on support for debugging.
This code has a bug and should fail the test. Let's start by using printf
debugging. Add some extra printfs to observe the state of the program as
it executes.

    :::c
    int avg( int x, int y )
    {
      printf( "x = %d, y = %d\n", x, y );
      int sum = x + x;
      printf( "sum = %d\n", sum );
      return sum / 2;
    }

You should be able to see that the value for the `sum` variable is
incorrect, but the value for the `x` and `y` variables are correct. This
means we can narrow our focus to line~4 in the above code snippet.
Hopefully, you should be able to spot the bug. Fix the bug, recompile,
and rerun the program.

Let's now try tracing the execution of this program using GDB. Then you
can start GDB like this

    :::bash
    % cd ${HOME}/ece2400/sec4
    % gdb -tui avg-test

GDB will drop you into a GDB "prompt" which you can use to interactively
execute your program. Your source code will show up at the top, and the
GDB prompt is at the bottom. Here are some useful GDB commands:

  - `break location` : set a breakpoint
  - `run` : start running the program
  - `record` : start recording the execution for reverse debugging
  - `step` : execute the next C statement, step into a function call
  - `next` : execute the next C statement, do not step into a function call
  - `rs` : reserve step, undo the execution of current C statement
  - `print var` : print a C variable
  - `continue` : continue on to the next breakpoint
  - `quit` : exit GDB
  - `refresh` : refresh the source code display

GDB is very sophisticated so of course there are many more commands you
can use, but these are enough to get started. Let's start by just running
the program in GDB:

    (gdb) run

Now let's try again, but first let's set a breakpoint to tell GDB to stop
at a certain function or line in the program. The following will set a
breakpoint at the beginning of the `main` function.

    (gdb) break main

You can see a little `B` marker in the margin next to the first statement
in the `main` function indicating the location of the breakpoint. We can
now use `run` to start the program running. The execution should stop at
the beginning of the function `main`. You should see the first line of
the function highlighted.

    (gdb) run

We can use `record` to turn on recording to enable reverse debugging and
then we can step through the execution of each C statement using the
`step` command.

    (gdb) record
    (gdb) step

Keep using `step` until you get into the `avg` function You can print out
the value of any variable using the `print` command:

    (gdb) print x
    (gdb) print y
    (gdb) print sum

You can also step backwards using the `rs` command:

    (gdb) rs

Try stepping forward and backward through the `avg` function and print
out various variables to see how they change during the execution. You
can use `quit` to exit.

    (gdb) quit

!!! question "To-Do On Your Own"

    Try compiling `gcd-test.c` and executing the resulting binary. The
    test should fail. Use GDB debugging to find the bug and fix it.

2. Using GCOV for Code Coverage
--------------------------------------------------------------------------

One you have developed your implementation and the corresponding basic,
directed, and random tests, you can then move on to understanding the
_quality_ of your current set of tests. One way to do this is to use code
coverage tools. The idea here is to use a tool which will count how many
times each line of code in your program is executed when running all of
your tests. If there are lines of code which have never executed, then
this is a good indicator that you need more tests to verify that part of
your code.

Let's start by recompiling our `avg-test.c` and turning on code coverage
support.

    :::bash
    % cd ${HOME}/ece2400/sec4
    % gcc -Wall -g --coverage -o avg-test avg-test.c
    % ./avg-test

This will generate additional data in `avg-test.gcda`. To make this data
easy to read and understand we need to run two more tools: `lcov` and
`genhtml` like this:

    :::bash
    % cd ${HOME}/ece2400/sec4
    % lcov --capture --directory . --output-file coverage.info
    % genhtml coverage.info --output-directory coverage-html

These tools will produce easy to read HTML reports. While you can use a
browser like `firefox`, it is faster (especially when working on the
server) to use a terminal based browser like `elinks`:

    :::bash
    % elinks coverage-html/index.html

You should be able to use your mouse to browse to the report for the
`avg-test.c` source file and verify that you are achieving 100% code
coverage. Note that 100% code coverage is not the same as 100% path
coverage; ask the instructors for more on this. Also note that 100% code
coverage does not mean your program is guaranteed to be correct!

!!! question "To-Do On Your Own"

    Try compiling `gcd-test.c` and executing the resulting binary with
    support for code coverage. Use the code coverage reports to verify
    that the test has less than 100% code coverage. Add more tests to
    improve the code coverage to 100%.

3. Using TravisCI and Codecov.io for Continuous Integration
--------------------------------------------------------------------------

Continuous integration is the process of _continually_ integrating,
testing, and evaluating your code. We will be using two tools to
facilitate continuous integration. The first is TravisCI, an online
service which is tightly coupled to GitHub. TravisCI will automatically
run all tests for a project every time code is pushed to GitHub. The
second is Codecov.io, an online service for visualizing code coverage.

To start, you need to enable TravisCI for the remote repository on
GitHub. Log into TravisCI using your GitHub ID and password:

 - https://travis-ci.org/profile

Once you have signed in, you should go to your TravisCI profile and find
the list of your public GitHub repositories. You may need to click Sync
to ensure that TravisCI has the most recent view of your public
repositories on GitHub. Turn on TravisCI with the little "switch" next to
the repository we have been using in this tutorial
(`githubid/ece2400-sec4-c-debug-coverage`). After enabling TravisCI for
the `githubid/ece2400-sec4-c-debug-coverage` repository, you should be
able to go to the TravisCI page for this repository:

 - https://travis-ci.org/githubid/ece2400-sec4-c-debug-coverage

TravisCI will report that there are no builds for this repository yet.
TravisCI looks for a special file named `.travis.yml` in the top of your
repository to determine how to build and test your project. We have
already created one of those files for you, and you can see it here:

    :::bash
    % cd ${HOME}/ece2400/sec4
    % cat .travis.yml

Go ahead and commit all of the work you have done in this tutorial, then
push your local commits to the remote repository on GitHub. If you
revisit the TravisCI page for this repository, you should see TravisCI
starting to build and run all of your tests. You can add a little badge
to your `README` to show if you are passing your tests.

Click on the badge on the TravisCI page corresponding for this repo and
choose "Markdown". Then cut-and-paste the code into your README for the
repo. Add the file, commit, and push.

Just like we can use TravisCI to automatically run tests, we can use
Codecov.io to automatically prepare code coverage reports on every
commit. You can see your code coverage reports here:

 - https://codecov.io/gh/githubid/ece2400-sec4-c-debug-coverage

You can get a badge for your code coverage too:

 - https://codecov.io/gh/githubid/ece2400-sec4-c-debug-coverage/settings/badge

4. Try Steps for Programming Assignments
--------------------------------------------------------------------------

Spend some time looking at the `.travis.yml` file for PA1. Look at the
TravisCI results for the PA1 milestone and also look at the Codecov.io
results.

