
Section 6: Sorting Lab
==========================================================================

In this discussion, we will implement, test, and evaluate a simple
insertion sort algorithm.

1. The ecelinux Machines
--------------------------------------------------------------------------

Follow the same process as in the previous sections.

 - login to a workstation with your NetID and password
 - use MobaXterm to log into the `ecelinux` servers
 - make sure you source the setup script
 - verify ECE2400 is in your prompt

Now clone the GitHub repo we will be using in this section using the
following commands:

    :::bash
    % source setup-ece2400.sh
    % mkdir -p ${HOME}/ece2400
    % cd ${HOME}/ece2400
    % git clone git@github.com:cornell-ece2400/ece2400-sec6 sec6
    % cd sec6
    % tree

The repository includes the following files:

```
.
├── CMakeLists.txt
├── src
│   ├── ece2400-stdlib.c
│   ├── ece2400-stdlib.h
│   ├── insertion-sort.h
│   ├── insertion-sort.c
│   └── insertion-sort-adhoc.c
├── test
│   ├── utst.h
│   └── insertion-sort-directed-test.c
└── eval
    ├── insertion-sort-eval.c
    └── sort.dat
```

2. Implementing Sorted Insert Helper Function
--------------------------------------------------------------------------

Take a look at the `insertion-sort.c` source file to find the
`sorted_insert` function is a helper function. It takes as input an
array, a range to work on (`begin` to `end`), and a value to insert. The
function can assume that the given range in the array is already sorted.
The function should insert the given value into the correct location of
the given range in order to preserve the invariant that this range of the
array remains sorted. Note that `end` should be the index of one past the
sorted region. `a[end]` must be a valid element in the array since after
we insert the given value the sorted region will increase by one element.

Go ahead and implement `sorted_insert` using your favorite text editor.
You can use either a forward (iterate from `begin` to `end`) or reverse
(iterate from `end` to `begin`) implementation. When you are finished use
adhoc testing to quickly see if your algorithm is basically working:

    :::bash
    % cd ${HOME}/sec6/src
    % gcc -Wall -o insertion-sort-adhoc insertion-sort-adhoc.c insertion-sort.c
    % ./insertion-sort-adhoc

3. Testing Sorted Insert Helper Function
--------------------------------------------------------------------------

Obviously, we want to do more than just ad-hoc testing. We have provided
you a directed test case to test just the helper function. Take a look at
this directed test in `insertion-sort-directed-test.c`. You can build and
run this test case like this:

    :::bash
    % cd ${HOME}/sec6
    % mkdir build
    % cd build
    % cmake ..
    % make insertion-sort-directed-test
    % ./insertion-sort-directed-test 1

Add at least one more directed test before continuing. Try and think of a
tricky corner case.

4. Implementing Insertion Sort
--------------------------------------------------------------------------

Take a look at the `insertion-sort.c` source file to find the
`insert_sort` function. It takes as input an array and the size of that
array. Go ahead and implement `insertion_sort` using your favorite text
editor. The function should use `sorted_insert` helper function, and you
can use either an out-of-place or in-place implementation. When you are
finished modify the adhoc test to test insertion sort, then quickly see
if your algorithm is basically working:

    :::bash
    % cd ${HOME}/sec6/src
    % gcc -Wall -o insertion-sort-adhoc insertion-sort-adhoc.c insertion-sort.c
    % ./insertion-sort-adhoc

5. Testing Insertion Sort
--------------------------------------------------------------------------

Obviously, we want to do more than just ad-hoc testing. We have provided
you a very simple directed test case to test insertion sort. Take a look
at this directed test in `insertion-sort-directed-test.c`. You can build
and run this test case like this:

    :::bash
    % cd ${HOME}/sec6/build
    % make insertion-sort-directed-test
    % ./insertion-sort-directed-test 2

Add at least one more directed test before continuing. Try and think of a
tricky corner case.

6. Evaluating Insertion Sort
--------------------------------------------------------------------------

We have provided you an evaluation program to quantitatively evaluating
the execution time and space usage of your insertion sort implementation.
Take a look at this evaluation program in `insertion-sort-eval.c`. You
can build and run this evaluation program like this:

    :::bash
    % cd ${HOME}/sec6
    % mkdir build-eval
    % cd build-eval
    % cmake -DCMAKE_BUILD_TYPE=eval ..
    % make insertion-sort-eval
    % ./insertion-sort-eval

You will see that the evaluation program takes two command line
arguments. The first is what pattern to use when generating the data for
the input array, and the second is the size of the input array. Let's do
an experiment to see how the execution time of your insertion sort
implementation grows as a function of the input array size for an input
array with random input values.

    :::bash
    % cd ${HOME}/sec6/build-eval
    % ./insertion-sort-eval urandom0 2000
    % ./insertion-sort-eval urandom0 4000
    % ./insertion-sort-eval urandom0 6000
    % ./insertion-sort-eval urandom0 8000
    % ./insertion-sort-eval urandom0 10000

You can use `seq` and `xargs` to run a command multiple times with
different parameters in a single step like this:

    :::bash
    % cd ${HOME}/sec6/build-eval
    % seq 2000 2000 10000 | xargs -l ./insertion-sort-eval urandom0

Copy down the size and average execution time for each experiment. Create
a plain text file named `data.txt` with one row per experiment and two
columns. The first column should be the size and the second column should
be the execution time. Then you can easily plot this data like this:

    :::bash
    % eplot data.txt

Do these quantitative results match your qualitative expectations given
what you know about the asymptotic time complexity of insertion sort?

