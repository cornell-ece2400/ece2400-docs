
Section 9: Static Polymorphism Lab
==========================================================================

In this discussion, we will implement, test, and evaluate a simple static
polymorphic list data structure using C++ and then compare it to a
dynamic polymorphic list and a monomorphic list.

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
    % git clone git@github.com:cornell-ece2400/ece2400-sec9 sec9
    % cd sec9
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
│   ├── SListDpoly.cc
│   ├── SListDpoly.h
│   ├── SListSpoly.cc
│   ├── SListSpoly.inl
│   ├── slist-int-adhoc.cc
│   ├── slist-dpoly-adhoc.cc
│   └── slist-spoly-adhoc.cc
├── test
│   ├── utst.h
│   ├── slist-int-directed-test.cc
│   ├── types-dpoly-directed-test.cc
│   ├── slist-dpoly-directed-test.cc
│   ├── slist-spoly-directed-test.cc
│   └── CMakeLists.txt
└── eval
    ├── slist-int-eval.cc
    ├── slist-dpoly-eval.cc
    └── slist-spoly-eval.cc
```

Take a look at the `SListSpoly.h` header file to understand all of the
public member functions we will be implementing in this lab.

  - `SListSpoly()`: default constructor
  - `~SListSpoly()`: destructor
  - `SListSpoly( const SListSpoly& lst )`: copy constructor
  - `SListSpoly& operator=( const SListSpoly& lst )`: assignment operator
  - `void push_front( const T& v )`: push item on front of list
  - `const T& at( size_t idx )`: return item at given index
  - `T& at( size_t idx )`: return item at given index
  - `void reverse()`: reverse all items in list
  - `void print() const`: print the list

2. Implementing the Constructor, Destructor, Push Front, Print
--------------------------------------------------------------------------

Let's start by implementing these functions:

  - `SListSpoly()`: default constructor
  - `~SListSpoly()`: destructor
  - `void push_front( const T& v )`: push item on front of list
  - `void print() const`: print the list

Feel free to review the lectures notes for help on how to implement these
functions. You might also want to look at how the monomorphic list was
implemented. When you are finished use adhoc testing to quickly see if
your data structure is basically working:

    :::bash
    % cd ${HOME}/sec9/src
    % g++ -Wall -o slist-spoly-adhoc slist-spoly-adhoc.cc
    % ./slist-spoly-adhoc

3. Testing Push Front
--------------------------------------------------------------------------

Obviously, we want to do more than just ad-hoc testing. We have provided
you a directed test case to test the basic functionality we have
implemented so far. Take a look at this directed test case in
`slist-spoly-directed-test.c`. You will need to implement two different
`at` member function to pass the very first directed test. One `at`
member function is used for reading items from the list, while the other
is used for modifying items in the list. Go ahead and implement both `at`
member functions and then build and run this test case like this:

    :::bash
    % cd ${HOME}/sec9
    % mkdir build
    % cd build
    % cmake ..
    % make slist-spoly-directed-test
    % ./slist-spoly-directed-test 1

4. Implementing Reverse
--------------------------------------------------------------------------

Take a look at the `SListSpoly.cc` source file to find the `reverse`
member function. This function should reverse all of the items in the
list. We recommend you use the following three steps:

 1. Iterate through the list to count the number of items
 2. Allocate a new array of integers on the heap with size items
 3. Iterate through the list and copy each item to the array
 4. Iterate through the list and copy each item from the array in reverse order

You are welcome to use a different approach, but the above is probably
the simplest. When you are finished use the corresponding directed test
case to verify your implementation is working:

    :::bash
    % cd ${HOME}/sec9/build
    % make slist-spoly-directed-test
    % ./slist-spoly-directed-test 2

5. Implementing the Copy Constructor and Assignment Operator
--------------------------------------------------------------------------

Because we need to implement a destructor, the rule of three tells us we
also need to implement a copy constructor and assignment operator. These
are simliar. The key difference is that the assignment operator must
first clean up the current object before doing the copy, while the copy
constructor can assume the current object is uninitialized. Implementing
copy can be a little tricky. The easiest (but not the most efficient)
algorithm is to just iterate through the given list and call `push_front`
for each item. Unfortunately, this will result in the new copy being in
the reverse order ... but then you can call the `reverse` method to make
sure the items are in the right order. Make sure you handle self
assignment correctly!

When you are finished use the corresponding directed test cases to verify
your implementation is working:

    :::bash
    % cd ${HOME}/sec9/build
    % make slist-spoly-directed-test
    % ./slist-spoly-directed-test 3
    % ./slist-spoly-directed-test 4
    % ./slist-spoly-directed-test 5

Run all of the test cases to make sure everything is now working:

    :::bash
    % cd ${HOME}/sec9/build
    % make slist-spoly-directed-test
    % ./slist-spoly-directed-test

6. Evaluating Push Front and Reverse
--------------------------------------------------------------------------

We have provided you an evaluation program to quantitatively evaluate the
execution time of the linked list data structure, push front, and the
reverse algorithm. Take a look at this evaluation program in
`list-spoly-eval.c`. You can build and run this evaluation program like
this:

    :::bash
    % cd ${HOME}/sec9
    % mkdir build-eval
    % cd build-eval
    % cmake -DCMAKE_BUILD_TYPE=eval ..
    % make slist-spoly-eval

You will see that the evaluation program takes one command line argument
specifying the size of the input array. Let's do an experiment to see how
the execution time of linked list data structure and reverse algorithm
grows as a function of the input array size.

    :::bash
    % cd ${HOME}/sec9/build-eval
    % ./slist-spoly-eval 10000
    % ./slist-spoly-eval 20000
    % ./slist-spoly-eval 30000
    % ./slist-spoly-eval 40000
    % ./slist-spoly-eval 50000

You can use `seq` and `xargs` to run a command multiple times with
different parameters in a single step like this:

    :::bash
    % cd ${HOME}/sec9/build-eval
    % seq 10000 10000 50000 | xargs -l ./slist-spoly-eval

Copy down the average execution time for each experiment. Create a plain
text file named `data-spoly.txt` with one row per experiment and a single
column which should be the execution time.

Let's also reproduce the data from the previous sections when we worked
on a monomorphic and dynamic polymorphic singly linked lists.

    :::bash
    % cd ${HOME}/sec9/build-eval
    % make eval
    % seq 10000 10000 50000 | xargs -l ./slist-int-eval
    % seq 10000 10000 50000 | xargs -l ./slist-dpoly-eval

Copy down the average execution time for each experiment. Create a plain
text file named `data-int.txt` and `data-dpoly.txt` with one row per
experiment and a single column which should be the execution time. Then
you can easily plot this data like this:

    :::bash
    % eplot -m data-int.txt data-dpoly.txt data-spoly.txt

Do these quantitative results match your qualitative expectations given
what you know about the asymptotic time complexity of push front and
reverse?

