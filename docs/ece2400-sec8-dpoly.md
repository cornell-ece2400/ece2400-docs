
Section 8: Dynamic Polymorphism Lab
==========================================================================

In this discussion, we will implement, test, and evaluate a simple
dynamic polymorphic list data structure using C++.

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
    % git clone git@github.com:cornell-ece2400/ece2400-sec8 sec8
    % cd sec8
    % tree

The repository includes the following files:

```
.
├── CMakeLists.txt
├── src
│   ├── ece2400-stdlib.cc
│   ├── ece2400-stdlib.h
│   ├── types-dpoly.h
│   ├── types-dpoly.cc
│   ├── SListInt.h
│   ├── SListInt.cc
│   ├── SListInt.inl
│   ├── SListIObj.cc
│   ├── SListIObj.h
│   ├── SListIObj.inl
│   ├── slist-int-adhoc.cc
│   └── slist-iobj-adhoc.cc
├── test
│   ├── utst.h
│   ├── types-dpoly-directed-test.cc
│   ├── slist-int-directed-test.cc
│   ├── slist-iobj-directed-test.cc
│   └── CMakeLists.txt
└── eval
    ├── slist-int-eval.cc
    └── slist-iobj-eval.cc
```

Take a look at the `SListIObj.h` header file to understand all of the
public member functions we will be implementing in this lab.

  - `SListIObj()`: default constructor
  - `~SListIObj()`: destructor
  - `SListIObj( const SListIObj& lst )`: copy constructor
  - `SListIObj& operator=( const SListIObj& lst )`: assignment operator
  - `void push_front( int v )`: push item on front of list
  - `int at( size_t idx )`: return item at given index
  - `void reverse()`: reverse all items in list
  - `void print() const`: print the list

We also include two private helper member functions:

  - `void copy( const SListIObj& lst )`: copy `lst` into this list
  - `void clear()`: clear this list

2. Implementing the Constructor, Destructor, Push Front, Print
--------------------------------------------------------------------------

Let's start by implementing these functions:

  - `SListIObj()`: default constructor
  - `~SListIObj()`: destructor
  - `void push_front( int v )`: push item on front of list
  - `void print() const`: print the list
  - `void clear()`: clear this list

The destructor simply calls `clear` so all you need to do is implement
`clear`. Feel free to review the lectures notes for help on how to
implement these functions. Make sure you delete not just the node but
also the dynamically allocated `IObject`! When you are finished use adhoc
testing to quickly see if your data structure is basically working:

    :::bash
    % cd ${HOME}/sec8/src
    % g++ -Wall -o slist-iobj-adhoc slist-iobj-adhoc.cc SListIObj.cc types-dpoly.cc
    % ./slist-iobj-adhoc

3. Testing Push Front
--------------------------------------------------------------------------

Obviously, we want to do more than just ad-hoc testing. We have provided
you a directed test case to test the basic functionality we have
implemented so far. Take a look at this directed test case in
`slist-iobj-directed-test.c`. You will need to implement the `at` member
function to pass the very first directed test. Go ahead and implement
`at` then build and run this test case like this:

    :::bash
    % cd ${HOME}/sec8
    % mkdir build
    % cd build
    % cmake ..
    % make slist-iobj-directed-test
    % ./slist-iobj-directed-test 1

4. Implementing Reverse
--------------------------------------------------------------------------

Take a look at the `SListIObj.cc` source file to find the `reverse` member
function. This function should reverse all of the items in the list. We
recommend you use the following three steps:

 1. Iterate through the list to count the number of items
 2. Allocate a new array of `IObject` pointers on the heap with size items
 3. Iterate through the list and copy each `IObject` pointer to the array
 4. Iterate through the list and copy each `IObject` pointer from the array in reverse order

You are welcome to use a different approach, but the above is probably
the simplest. When you are finished use the corresponding directed test
case to verify your implementation is working:

    :::bash
    % cd ${HOME}/sec8/build
    % make slist-iobj-directed-test
    % ./slist-iobj-directed-test 2

5. Implementing the Copy Constructor and Assignment Operator
--------------------------------------------------------------------------

Because we need to implement a destructor, the rule of three tells us we
also need to implement a copy constructor and assignment operator. One
easy way to implement the destructor, copy constructor, assignment
operator is to first implement two private helper member functions:

 - `void copy( const SListIObj& lst )`: copy `lst` into this list
 - `void clear()`: clear this list

Then we can just call `copy` and `clear` appropriately like this:

 - destructor calls `clear`
 - copy constructor calls `copy`
 - assignment operator calls `clear` then `copy`

Implementing copy can be a little tricky. The easiest (but not the most
efficient) algorithm is to just iterate through the given list and call
`push_front` for each item. Unfortunately, this will result in the new
copy being in the reverse order ... but then you can call the `reverse`
method to make sure the items are in the right order.

When you are finished use the corresponding directed test cases to verify
your implementation is working:

    :::bash
    % cd ${HOME}/sec8/build
    % make slist-iobj-directed-test
    % ./slist-iobj-directed-test 3
    % ./slist-iobj-directed-test 4

Run all of the test cases to make sure everything is now working:

    :::bash
    % cd ${HOME}/sec8/build
    % make slist-iobj-directed-test
    % ./slist-iobj-directed-test

6. Evaluating Reverse
--------------------------------------------------------------------------

We have provided you an evaluation program to quantitatively evaluate the
execution time of the linked list data structure and reverse algorithm.
Take a look at this evaluation program in `list-iobj-eval.c`. You
can build and run this evaluation program like this:

    :::bash
    % cd ${HOME}/sec8
    % mkdir build-eval
    % cd build-eval
    % cmake -DCMAKE_BUILD_TYPE=eval ..
    % make slist-iobj-eval

You will see that the evaluation program takes one command line argument
specifying the size of the input array. Let's do an experiment to see how
the execution time of linked list data structure and reverse algorithm
grows as a function of the input array size.

    :::bash
    % cd ${HOME}/sec8/build-eval
    % ./slist-iobj-eval 10000
    % ./slist-iobj-eval 20000
    % ./slist-iobj-eval 30000
    % ./slist-iobj-eval 40000
    % ./slist-iobj-eval 50000

You can use `seq` and `xargs` to run a command multiple times with
different parameters in a single step like this:

    :::bash
    % cd ${HOME}/sec8/build-eval
    % seq 10000 10000 50000 | xargs -l ./slist-iobj-eval

Copy down the size and average execution time for each experiment. Create
a plain text file named `data-iobj.txt` with one row per experiment and
two columns. The first column should be the size and the second column
should be the execution time.

Let's also reproduce the data from last week when we worked on a
(non-polymorphic) object-oriented singly linked list.

    :::bash
    % cd ${HOME}/sec8/build-eval
    % make slist-int-eval
    % ./slist-int-eval 10000
    % ./slist-int-eval 20000
    % ./slist-int-eval 30000
    % ./slist-int-eval 40000
    % ./slist-int-eval 50000

Again, you can use `seq` and `xargs` to run a command multiple times with
different parameters in a single step like this:

    :::bash
    % cd ${HOME}/sec8/build-eval
    % seq 10000 10000 50000 | xargs -l ./slist-int-eval

Copy down the size and average execution time for each experiment. Create
a plain text file named `data-int.txt` with one row per experiment and
two columns. The first column should be the size and the second column
should be the execution time. Then you can easily plot this data like
this:

    :::bash
    % eplot -m data-iobj.txt data-int.txt

Do these quantitative results match your qualitative expectations given
what you know about the asymptotic time complexity of reverse?

