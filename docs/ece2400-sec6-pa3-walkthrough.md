Section 6: ECE 2400 PA 3 Walkthrough
====================================

In this discussion, we will explore two different implementations of a simple
sorting algorithm called **insertion sort**. We will use similar build, test,
and evaluation infrastructure as you will use in PA 3. Hopefully, after this
section, you can start working on your PA 3.

Follow the same process as in the previous discussion section. You need to login
to a workstation with your NetID and password. Start a terminal and then don't
forget to source the setup script!

```
% source setup-ece2400.sh
```

Now clone the github repo for this discussion section. No need to fork the repo,
just clone it.

```
% mkdir -p ${HOME}/ece2400
% cd ${HOME}/ece2400
% git clone git@github.com:cornell-ece2400/ece2400-pa3-walkthrough.git sec6
% cd sec6
% ls
```

The repo includes the following files:

- CMake configuration script:
  - CMakeLists.txt
- Header file, source file, and ad-hoc test for out-of-place insertion sort implementation:
  - src/oop-insertion-sort.h
  - src/oop-insertion-sort.c
  - src/oop-insertion-sort-main.c
- Header file, source file, and ad-hoc test for in-place insertion sort implementation:
  - src/ip-insertion-sort.h
  - src/ip-insertion-sort.c
  - src/ip-insertion-sort-main.c
- Test files for both implementations
  - tests/oop-insertion-sort-basic-tests.c
  - tests/oop-insertion-sort-directed-tests.c
  - tests/oop-insertion-sort-random-tests.c
  - tests/ip-insertion-sort-basic-tests.c
  - tests/ip-insertion-sort-directed-tests.c
  - tests/ip-insertion-sort-random-tests.c
- Files containing utility functions:
  - src/utils.h
  - src/utils.c
  - tests/utst.h
- Performance evaluation files:
  - src/sort-eval.c
  - src/sort.dat

You will implement two versions of the insertion sort algorithm. The first
version is called **out-of-place insertion sort (oop-insertion-sort)** that uses
extra temporary space. The second version is called **in-place insertion sort
(ip-insertion-sort)** that does NOT use extra temporary space to sort a given
array. Here are the two functions you are about to implement:

```
void oop_insertion_sort( int arr[], size_t size );
void ip_insertion_sort ( int arr[], size_t size );
```

Both functions take as input an integer array _arr_ with length _size_ and sort
the array in an ascending order.

---

1. Out-of-place insertion sort (oop-insertion-sort)

The intuition behind insertion sort algorithm is simple. Let's say we have an
array of numbers sorted in an ascending order. We want to insert a number to the
array and keep the array still sorted after the insertion. How would we do that?

Let's look at the following example.

```
array = { 1, 4, 6, 10, 15 }
value = 5
```

We want to insert _value_ to _array_. One way to do the insertion is that we
look for the correct position of _value_ in _array_, which is in between _4_ and
_6_, insert _5_ in that position, and shift the rest of the array to the right
by one position. We can do that using the following algorithm.

```
insert( arr, size, value ):
  make an empty array called tmp_arr of size (size + 1)
  for i in 0 to size - 1 (inclusive)
    if value < tmp_arr[i]
      tmp_arr[i] = value
      value = arr[i]
    else
      tmp_arr[i] = arr[i]
  tmp_arr[size] = value
  return tmp_arr
```

We can leverage the above algorithm in our insertion sort by taking each element
out of the input array and insert it into a sorted output array such that the
output array remains sorted. Here is the pseudocode:

```
oop_insertion_sort( arr, size ):
  make an empty array called tmp_arr of size "size"
  for i in 0 to size - 1 (inclusive)
    tmp_arr[i] = arr[i]
    for j in 0 to i - 1 (inclusive)
      if tmp_arr[i] < tmp_arr[j]
        swap tmp_arr[i] and tmp_arr[j]
  copy tmp_arr to arr
```

This algorithm is out-of-place since we need an extra array of the same size to
temporarily store sorted elements.

**Your tasks**

- Implement the above algorithm in `oop_insertion_sort()` function in `src/oop_insertion_sort.c`
- Add directed and random tests to test your implementation
- Analyze the time and space complexity of your implementation

---

2. In-place insertion sort (ip-insertion-sort)

We can do better than out-of-place insertion sort in terms of space complexity
by not using the temporary array. Instead, we can sort all elements by swapping
them directly in the original array (i.e., in place). Here is the pseudocode:

```
ip_insertion_sort( arr, size )
  for i in 0 to size - 1 (inclusive)
    for j in 0 to i - 1 (inclusive)
      if arr[i] < arr[j]
        swap arr[i] and arr[j]
```

This algorithm is in-place since we do NOT need an extra array of the same size
to temporarily store sorted elements.

**Your tasks**

- Implement the above algorithm in `ip_insertion_sort()` function in `src/ip_insertion_sort.c`
- Add directed and random tests to test your implementation
- Analyze the time and space complexity of your implementation

---

3. Evaluate both implementations

**Your tasks**
- Run the evaluation program to evaluate the performance of both implementations

```
% cd ${HOME}/ece2400/sec6
% mkdir -p build-eval
% cmake .. -DCMAKE_BUILD_TYPE=RELEASE
% make eval
```
