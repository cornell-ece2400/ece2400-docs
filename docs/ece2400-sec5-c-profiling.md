
Section 5: C Profiling
==========================================================================

In this discussion, we will explore how to measure the performance of
a C evaluation program.

Follow the same process as in the previous discussion section. You need
to login to a workstation with your NetID and password. Start a terminal
and then don't forget to source the setup script!

    :::bash
    % source setup-ece2400.sh

Now clone the github repo for this discussion section. No need to fork
the repo, just clone it.

    :::bash
    % mkdir -p ${HOME}/ece2400
    % cd ${HOME}/ece2400
    % git clone git@github.com:cornell-ece2400/ece2400-sec5-c-profiling sec5
    % cd sec5
    % ls

1. Warmup: Implement Array Average Functions
--------------------------------------------------------------------------

Take a look at the `array-eval.c` source file. You will see four
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

2. Measuring Execution Time
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

Modify the evaluation program to measure how long one experiment takes.
You will notice that the execution time is very short ... so short that
it is too fast for the resolution of the timer. We need to run a subtrial
in a loop many times to make sure we have a long enough experiment that
we can get a reasonable accurate time measurement. Put the subtrial in a
loop like this:

    :::c
    int x;
    int y;

    for ( int j = 0; j < 100000; j++ ) {

      int array[size];
      init_array( array, size );

      int* parray[size];
      init_parray( parray, array, size );

      x = avg_array( array, size );
      y = avg_parray( parray, size );

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

3. Profiling Execution Time
--------------------------------------------------------------------------

The previous section enables us to measure the overall execution time,
but we might also be interested to know which functions are taking the
most time. This can help us focus on the important hotspots for
optimization. We can use _profiling_ to do this kind of performance
analysis. We will look at two profiling tools: `gprof` and `perf`.

Let's start by recompiling our program with support for profiling:

    :::bash
    % cd ${HOME}/ece2400/sec5
    % gcc -Wall -pg -o array-eval array-eval.c

Notice the `-pg` command line option. This tells GCC to enable support
for profiling. Now we run the program and use the `gprof` tool to analyze
the execution time:

    :::bash
    % cd ${HOME}/ece2400/sec5
    % ./array-eval
    % gprof ./array-eval

The output will have two parts: a flat profile and a call graph profile.
The flat profile specifies how many times each function was called and
how much time was spent in each function. The call graph profile
additionally indicates what exact sequence of function calls led let to a
specific function call, and how much time was spent in that specific
function call. Why do you think more time is spent in `init_array`?

Let's now use a different tool called `perf`:

    :::bash
    % cd ${HOME}/ece2400/sec5
    % perf record ./array-eval
    % perf report --stdio

The output will show a flat profile, but it also includes how much time
was spent in various functions contained in the standard C library. Does
this information help explain why more time is spent in `init_array`?

We can use these profiling tools to help identify hotspots in our code
for optimization. Hotspots might be due to a small function which is
called many, many times, or a function which is only called a few times
but takes a very long time to execute.

