
Section 7: Sorting Lab
==========================================================================

In this discussion, we will implement, test, and evaluate a simple
insertion sort algorithm.

1. The ecelinux Machines
--------------------------------------------------------------------------

Follow the same process as in previous sections. If you are participating
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
    % git clone git@github.com:cornell-ece2400/ece2400-sec7 sec7
    % cd sec7
    % tree

The repository includes the following files:

```
.
├── CMakeLists.txt
├── src
│   ├── ece2400-stdlib.c
│   ├── ece2400-stdlib.h
│   ├── isort-int.h
│   ├── isort-int.c
│   └── isort-int-adhoc.c
├── test
│   ├── utst.h
│   └── isort-int-directed-test.c
└── eval
    ├── isort-int-eval.c
    └── sort.dat
```

2. Implementing Sorted Insert Helper Function
--------------------------------------------------------------------------

Take a look at the `isort-int.c` source file to find the `sorted_insert`
function is a helper function. It takes as input an array, a range to
work on (`begin` to `end`), and a value to insert. The function can
assume that the given range in the array is already sorted. The function
should insert the given value into the correct location of the given
range in order to preserve the invariant that this range of the array
remains sorted. Note that `end` should be the index of one past the
sorted region. `a[end]` must be a valid element in the array since after
we insert the given value the sorted region will increase by one element.

Go ahead and implement `sorted_insert` using your favorite text editor.
You can use either a forward (iterate from `begin` to `end`) or reverse
(iterate from `end` to `begin`) implementation. When you are finished use
adhoc testing to quickly see if your algorithm is basically working:

    :::bash
    % cd ${HOME}/sec7/src
    % gcc -Wall -o isort-int-adhoc ece2400-stdlib.c isort-int.c isort-int-adhoc.c
    % ./isort-int-adhoc

3. Testing Sorted Insert Helper Function
--------------------------------------------------------------------------

Obviously, we want to do more than just ad-hoc testing. We have provided
you a directed test case to test just the helper function. Take a look at
this directed test in `isort-int-directed-test.c`. You can build and run
this test case like this:

    :::bash
    % cd ${HOME}/sec7
    % mkdir build
    % cd build
    % cmake ..
    % make isort-int-directed-test
    % ./isort-int-directed-test 1

Add at least one more directed test before continuing. Try and think of a
tricky corner case.

4. Implementing Insertion Sort
--------------------------------------------------------------------------

Take a look at the `isort-int.c` source file to find the `isort_int`
function. It takes as input an array and the size of that array. Go ahead
and implement `isort_int` using your favorite text editor. The function
should use `sorted_insert` helper function, and you can use either an
out-of-place or in-place implementation. When you are finished modify the
adhoc test to test insertion sort, then quickly see if your algorithm is
basically working:

    :::bash
    % cd ${HOME}/sec7/src
    % gcc -Wall -o isort-int-adhoc ece2400-stdlib.c isort-int.c isort-int-adhoc.c
    % ./isort-int-adhoc

Or feel free to just move on to using the test framework in the next
section.

5. Testing Insertion Sort
--------------------------------------------------------------------------

Obviously, we want to do more than just ad-hoc testing. We have provided
you a very simple directed test case to test insertion sort. Take a look
at this directed test in `isort-int-directed-test.c`. You can build and
run this test case like this:

    :::bash
    % cd ${HOME}/sec7/build
    % make isort-int-directed-test
    % ./isort-int-directed-test 2

Add at least one more directed test before continuing. Try and think of a
tricky corner case.

6. Evaluating Insertion Sort
--------------------------------------------------------------------------

We have provided you an evaluation program to quantitatively evaluating
the execution time and space usage of your insertion sort implementation.
Take a look at this evaluation program in `isort-int-eval.c`. You can
build and run this evaluation program like this:

    :::bash
    % cd ${HOME}/sec7
    % mkdir build-eval
    % cd build-eval
    % cmake -DCMAKE_BUILD_TYPE=eval ..
    % make isort-int-eval
    % ./isort-int-eval

You will see that the evaluation program takes two command line
arguments. The first is what pattern to use when generating the data for
the input array, and the second is the size of the input array. Let's do
an experiment to see how the execution time of your insertion sort
implementation grows as a function of the input array size for an input
array with random input values.

    :::bash
    % cd ${HOME}/sec7/build-eval
    % ./isort-int-eval urandom0 2000
    % ./isort-int-eval urandom0 4000
    % ./isort-int-eval urandom0 6000
    % ./isort-int-eval urandom0 8000
    % ./isort-int-eval urandom0 10000

You can use a Bash for loop to run a command multiple times with
different parameters in a single step like this:

    :::bash
    % cd ${HOME}/sec7/build-eval
    % for i in {1000,2000,4000,6000,8000,10000}; do ./isort-int-eval urandom0 $i; done

Copy down the size and average execution time for each experiment. Create
a plain text file named `data.txt` with one row per experiment and two
columns. The first column should be the size and the second column should
be the execution time. Then you can easily plot this data like this:

    :::bash
    % eplot data.txt

If you are using VS Code, then generate a PNG of the plot like this:

    :::bash
    % eplot --png -o plot.png data.txt

Then you can download and open the PNG file on your local workstation or
laptop. Do these quantitative results match your qualitative expectations
given what you know about the asymptotic time complexity of insertion
sort?

