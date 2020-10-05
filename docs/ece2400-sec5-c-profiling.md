
Section 5: C Profiling
==========================================================================

In this discussion, we will explore how to measure the performance of a C
evaluation program and deal with dynamic memory issues.

1. The ecelinux Machines
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
    % git clone git@github.com:cornell-ece2400/ece2400-sec5 sec5
    % cd sec5
    % tree

The repository includes the following files:

 - `stack-array-eval.c`: source and main for using an array of elements on stack
 - `heap-array-eval.c`: source and main for using an array of elements on heap

2. Warmup: Implement Array Average Functions
--------------------------------------------------------------------------

Take a look at the `stack-array-eval.c` source file. You will see four
functions:

 - `init_array` : initialize an array of integers with random values
 - `avg_array` : find average of an array of integers
 - `init_parray` : initialize an array of pointers to integers
 - `avg_parray` : find average of an array of pointers to integers

The first two functions operate on an array of integers, while the second
two functions operate on an array of _pointers_ to integers. Start by
sketching a state diagram for the given main program assuming `size` is
set to 3. Work through the state diagram and stop when you get to the
call to the `avg_array` function. Use this state diagram to understand
the difference between the array of integers vs. the array of pointers to
integers.

Now implement the `avg_array` function which should find the average of
the integers stored in the given array. Then implement the `avg_parray`
function which should find the average of the integers _pointed to_ by
the given array. Compile and execute your program a couple of times,
changing the seed passed into `srand` each time. Verify that the value
returned by `avg_array` always equals the value returned by `avg_parray`
and that the average is always around 500.

3. Measuring Execution Time
--------------------------------------------------------------------------

Now assume we want to quantitatively measure how long it takes to
initialize both arrays and then calculate the averages. To do this, we
can use the time functions provided by the C standard library in the
`sys/time.h` header file. Go ahead and add the following header to your
evaluation program:

    :::c
    #include <sys/time.h>

We can use the `gettimeofday` function to get the current time:

 - <http://man7.org/linux/man-pages/man2/gettimeofday.2.html>

This function takes as a parameter a pointer to a struct of type `struct
timeval`. It uses call-by-pointer semantics to update this struct with
the current time with a precision of 10s of microseconds. The struct has
two fields: `tv_sec` is the number of seconds and `tv_usec` is the number
of microseconds since January 1, 1970. We can use `gettimeofday` like
this to quantitatively measure how long it takes to run an experiment.

    :::c
    // Track time using timers

    struct timeval start;
    struct timeval end;

    // Start tracking time

    gettimeofday( &start, NULL );

    // Run the experiment

    // ...

    // Stop tracking time

    gettimeofday( &end, NULL );

    // Calculate elapsed time

    double elapsed = ( end.tv_sec - start.tv_sec ) +
                   ( ( end.tv_usec - start.tv_usec ) / 1000000.0 );

    printf( "Elapsed time for trial is %f\n", elapsed );

Modify the `stack-array-eval.c` program to measure how long one
experiment takes, compile the program with optimizations, and then run
the experiment.

    :::bash
    % cd ${HOME}/ece2400/sec5
    % gcc -Wall -o stack-array-eval stack-array-eval.c
    % ./stack-array-eval

You will notice that the execution time is very short ... so short that
it is too fast for the resolution of the timer. We need to run a subtrial
in a loop many times to make sure we have a long enough experiment that
we can get a reasonable accurate time measurement. Put the subtrial in a
loop like this:

    :::c
    int x;
    int y;

    for ( int j = 0; j < 100000; j++ ) {

      int array[1000];
      init_array( array, 1000 );

      int* parray[1000];
      init_parray( parray, array, 1000 );

      x = avg_array( array, 1000 );
      y = avg_parray( parray, 1000 );

    }

Your program should now run for a couple of seconds and this should
enable a much more precise time measurement. Try running the program at
least five times and write down the results for each trial. Is the
execution time always the same? If not, why not?

We need to do several trials and then take the average execution time to
ensure we can get a good estimate of the execution time. Restructure your
evaluation program to look like this:

    int main( void)
    {
      int ntrials    = 5;
      int nsubtrials = 1e5;

      double elapsed_avg = 0.0;

      for ( int i = 0; i < ntrials; i++ ) {

        // Track time using timers

        struct timeval start;
        struct timeval end;

        // Start tracking time

        gettimeofday( &start, NULL );

        // Run the experiment

        for ( int j = 0; j < nsubtrials; j++ ) {

          // ... run one trial ...

        }

        // Stop tracking time

        gettimeofday( &end, NULL );

        // Calculate elapsed time

        double elapsed = ( end.tv_sec - start.tv_sec ) +
                       ( ( end.tv_usec - start.tv_usec ) / 1000000.0 );

        elapsed_avg += elapsed;

        printf( "Elapsed time for trial %d is %f\n", i, elapsed );
      }

      // Calculate average elapsed time per trial

      elapsed_avg = elapsed_avg / ntrials;

      printf( "Elapsed time (averaged) is %f\n", elapsed_avg );
    }

Now use your evaluation program to quantitatively measure the execution
time of this experiment.

!!! note "To-Do On Your Own"

    Experiment with turning optimizations on with the `-O3` command line
    option. Compare the performance of this experiment with and without
    optimizations.

4. Profiling Execution Time
--------------------------------------------------------------------------

The previous section enables us to measure the overall execution time,
but we might also be interested to know which functions are taking the
most time. This can help us focus on the important hotspots for
optimization. We can use _profiling_ to do this kind of performance
analysis. We will be using the `perf` tool to do this kind of profiling.
It is important to turn debugging on when doing profiling so we can track
the function call stacks. Combining the debugging (`-g`) with
optimizations (`-O3`) will not slow down your program, but may
occassionally produce some confusing situations when debugging or
profiling since some of the original program might have been optimized
away.

Let's start by recompiling our program with support for debugging:

    :::bash
    % cd ${HOME}/ece2400/sec5
    % gcc -Wall -g -o stack-array-eval stack-array-eval.c

We can now use `perf` to profile our program:

    :::bash
    % cd ${HOME}/ece2400/sec5
    % perf record --call-graph dwarf ./stack-array-eval

`perf` works by interrupts your program several times throughout the
execution to take a "sample". During each sample, `perf` will stop your
program and examine the stack to see the list of function stack frames
that are currently allocated on the stack. `perf` will keep counters of
every call stack it sees. After `perf` is done it will output the total
number samples. If this number is too small then the profiling
information may not be very accurate. We can display this information
like this:

    :::bash
    % cd ${HOME}/ece2400/sec5
    % perf script report stackcollapse
    stack-array-eval;_start;__libc_start_main;main 78
    stack-array-eval;_start;__libc_start_main;main;init_array 8731
    stack-array-eval;_start;__libc_start_main;main;init_array;rand 1416
    stack-array-eval;_start;__libc_start_main;main;init_array;rand;__random 4239
    stack-array-eval;_start;__libc_start_main;main;init_array;rand;__random;__random_r 6959
    stack-array-eval;_start;__libc_start_main;main;init_array;rand@plt 4
    stack-array-eval;main 6
    stack-array-eval;main;avg_array 6025
    stack-array-eval;main;avg_parray 5230
    stack-array-eval;main;init_parray 6693

There is one line for every unique call stack which was sampled. The line
shows the name of the call stack and how many of the samples were taken
with that call stack. In the above example, 8731 samples where taken with
the call stack: `main` calling `init_array`. `perf` can also provide a
more interactive view to explore this profile information.

    :::bash
    % cd ${HOME}/ece2400/sec5
    % perf report

The report shows a line for various functions that consumed a significant
amount of the execution time. The _Childen_ column is the total time
consumed in that function _including_ any time spent in that function's
children. The _Self_ column is the time spent just in that function
_excluding_ any time spent in that function's children. The percentages
are all a percentage of the total execution time. You can use the arrow
keys to slect a function and you can press enter to show what functions
called this function and to see more about the children of this function.

You can also generate a flame graph, which is a very useful way to
interactively visualize this profiling information:

    :::bash
    % cd ${HOME}/ece2400/sec5
    % perf script report stackcollapse | flamegraph.pl > graph.svg
    % firefox graph.svg

In a flame graph the x-axis shows the stack profile population, sorted
alphabetically and the y-axis shows the stack depth counting form zero at
the bottom. Each rectangle represents a stack frame. The wider a frame is
is, the more often it was present in the stacks. The top edge shows what
function was executing when the sample was taken, and beneath it is its
ancestry. The colors are usually not significant, picked randomly to
differentiate frames. See this great article for more on flame graphs:

 - <https://queue.acm.org/detail.cfm?id=2927301>

!!! note "To-Do On Your Own"

    Experiment with turning optimizations on with the `-O3` command line
    option. Compare the profiling data for this experiment with and
    without optimizations.

5. Profiling Memory Usage
--------------------------------------------------------------------------

The previous sections focused on performance, but we also care about
memory usage. Valgrind is a tool specifically designed for memory
debugging, memory leak detection, and memory profiling. Take a look at
the `heap-array-eval.c` file. Compile and run this file to test its
performance. Unfortunately, this program has a memory leak since the
arrays are allocated on the heap by never deallocated. We can use
valgrind to detect this leak:

    :::bash
    % cd ${HOME}/ece2400/sec5
    % gcc -Wall -o heap-array-eval heap-array-eval.c
    % ./heap-array-eval
    % valgrind --leak-check=yes ./heap-array-eval

Unfortuntaely, Valgrind can slow your program down by a factor of 10x. So
to speed things up, change the `nsubtrials` to be 1. Valgrind will report
the memory leak and even identify the line in the code which allocated
the variables which were then never deallocated. Add an approriate call
to `free` to fix the memory leak, and then rerun Valgrind to verify that
the problem has been fixed.

