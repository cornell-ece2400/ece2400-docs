
Section 8: Object-Oriented Programming Lab
==========================================================================

In this discussion, we will implement, test, and evaluate a simple
list data structure using C++.

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
    % git clone git@github.com:cornell-ece2400/ece2400-sec8 sec8
    % cd sec8
    % tree

The repository includes the following files:

```
.
├── CMakeLists.txt
├── src
│   ├── ece2400-stdlib.h
│   ├── ece2400-stdlib.cc
│   ├── SListInt.h
│   ├── SListInt.inl
│   ├── SListInt.cc
│   └── slist-int-adhoc.cc
├── test
│   └── slist-int-directed-test.cc
└── eval
    └── slist-int-eval.cc
```

Take a look at the `SListInt.h` header file to understand all of the
public member functions we will be implementing in this lab.

  - `SListInt()`: default constructor
  - `~SListInt()`: destructor
  - `SListInt( const SListInt& lst )`: copy constructor
  - `SListInt& operator=( const SListInt& lst )`: assignment operator
  - `void push_front( int v )`: push item on front of list
  - `int at( size_t idx )`: return item at given index
  - `void reverse()`: reverse all items in list
  - `void print() const`: print the list

2. Implementing the Constructor, Destructor, Push Front
--------------------------------------------------------------------------

Let's start by implementing these functions:

  - `SListInt()`: default constructor
  - `~SListInt()`: destructor
  - `void push_front( int v )`: push item on front of list

Feel free to review the lectures notes for help on how to implement these
functions. We have already implemented `print` for you. When you are
finished use adhoc testing to quickly see if your data structure is
basically working:

    :::bash
    % cd ${HOME}/sec8/src
    % g++ -Wall -o slist-int-adhoc ece2400-stdlib.c SListInt.cc slist-int-adhoc.cc
    % ./slist-int-adhoc

3. Testing Push Front
--------------------------------------------------------------------------

Obviously, we want to do more than just ad-hoc testing. We have provided
you a directed test case to test the basic functionality we have
implemented so far. Take a look at this directed test case in
`slist-int-directed-test.c`. You will need to implement the `at` member
function to pass the very first directed test. Go ahead and implement
`at` then build and run this test case like this:

    :::bash
    % cd ${HOME}/sec8
    % mkdir build
    % cd build
    % cmake ..
    % make slist-int-directed-test
    % ./slist-int-directed-test 1

Now add a new test case for push front. Try pushing more than three
values into the list. Number this test case 6 and run it like this:

    :::bash
    % cd ${HOME}/sec8/build
    % make slist-int-directed-test
    % ./slist-int-directed-test 6

4. Implementing Reverse
--------------------------------------------------------------------------

Take a look at the `SListInt.cc` source file to find the `reverse` member
function. This function should reverse all of the items in the list. We
recommend you use the following steps:

 1. Iterate through the list to count the number of items
 2. Allocate a temporary array of integers on the heap with size items
 3. Iterate through the list and copy each item to the array
 4. Iterate through the list and copy each item from the array in reverse order
 5. Deallocate the temporary array

You are welcome to use a different approach, but the above is probably
the simplest. When you are finished use the corresponding directed test
case to verify your implementation is working:

    :::bash
    % cd ${HOME}/sec8/build
    % make slist-int-directed-test
    % ./slist-int-directed-test 2

Now add a new test case for reverse. Try pushing more than three values
into the list before calling reverse. Try reversing twice and verifying
the list has the original values. Number this test case 7 and run it like
this:

    :::bash
    % cd ${HOME}/sec8/build
    % make slist-int-directed-test
    % ./slist-int-directed-test 7

5. Implementing the Copy Constructor and Assignment Operator
--------------------------------------------------------------------------

Because we need to implement a destructor, the rule of three tells us we
also need to implement a copy constructor and assignment operator. These
are similar. The key difference is that the assignment operator must
first clean up the current object before doing the copy, while the copy
constructor can assume the current object is uninitialized. Implementing
the copy can be a little tricky. The easiest (but not the most efficient)
algorithm is to just iterate through the given list and call `push_front`
for each item. Unfortunately, this will result in the new copy being in
the reverse order ... but then you can call the `reverse` method to make
sure the items are in the right order. We have gone ahead and implemented
the copy constructor and assignment operator. Take a close look and then
verify these implementations work:

    :::bash
    % cd ${HOME}/sec8/build
    % make slist-int-directed-test
    % ./slist-int-directed-test 3
    % ./slist-int-directed-test 4

Run all of the test cases to make sure everything is now working:

    :::bash
    % cd ${HOME}/sec8/build
    % make slist-int-directed-test
    % ./slist-int-directed-test

You should see that the test case for self assignment is failing. Modify
the overloaded assignment operator to correctly handle self assignment
and then verify your implementation can pass all of the tests.

6. Evaluating Reverse
--------------------------------------------------------------------------

We have provided you an evaluation program to quantitatively evaluate the
execution time of the linked list data structure and reverse algorithm.
Take a look at this evaluation program in `list-int-eval.c`. You
can build and run this evaluation program like this:

    :::bash
    % cd ${HOME}/sec8
    % mkdir build-eval
    % cd build-eval
    % cmake -DCMAKE_BUILD_TYPE=eval ..
    % make slist-int-eval

You will see that the evaluation program takes one command line argument
specifying the size of the input array. Let's do an experiment to see how
the execution time of the reverse algorithm grows as a function of the
input array size.

    :::bash
    % cd ${HOME}/sec8/build-eval
    % ./slist-int-eval 10000
    % ./slist-int-eval 20000
    % ./slist-int-eval 30000
    % ./slist-int-eval 40000
    % ./slist-int-eval 50000

You can use `seq` and `xargs` to run a command multiple times with
different parameters in a single step like this:

    :::bash
    % cd ${HOME}/sec8/build-eval
    % for i in {10000,20000,30000,40000,50000}; do ./slist-int-eval $i; done

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
given what you know about the asymptotic time complexity of reverse?

