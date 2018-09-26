How to debug memory problems?
=============================

Author: Tuan Ta  
Date  : Sep 26, 2018

While dynamic memory allocation gives us a lot of freedom in keeping some blocks
of memory alive across function calls, misusing dynamically allocated memory is
often the most common cause of memory corruptions (e.g., segmentation fault). In
this tutorial, I'll walk you through some common mistakes in using dynamic
memory allocation and pointer. For each mistake, I'll show you how to detect it
using a powerful memory checking tool called _valgrind_.

## 1. Memory leak

Let's consider this program:

```
 1 #include <stdlib.h>
 2 #include <stdio.h>
 3
 4 int main( void ) {
 5
 6   // Allocate an int on the heap
 7   int* mem_ptr = ( int* ) malloc( sizeof( int) );
 8
 9   // Initialize that int variable
10   *mem_ptr = 10;
11
12   printf(" Before: mem_ptr: address = %lx, value = %d\n", mem_ptr, *mem_ptr);
13
14   // Declare a new int variable on the stack
15   int a = 20;
16
17   // Reuse mem_ptr to point to "a"
18   mem_ptr = &a;
19
20   printf(" After: mem_ptr: address = %lx, value = %d\n", mem_ptr, *mem_ptr);
21
22   // How do I get back my memory on the heap?
23
24   return 0;
25 }
```

We first allocate a block of memory on the heap (in line 7). Then, we assign
mem_ptr to point to a different block of memory on the stack (in line 18). By
assigning "mem_ptr" to a different block of memory, we lose the only way to go
back to our heap memory block. Therefore, in line 22, we cannot free that heap
memory block.

There're actually two problems here:

- First, since there is no pointer pointing to the block of memory on the heap,
that block of memory becomes "orphan".

- Second, since we do not or cannot free the block of memory, we lose it in our
program. This problem is called "memory leak".

Now, let's compile and run the program:

```
ECE2400: ~/ece2400/tests % gcc -Wall -g -O3 -o mem-leak mem-leak.c
ECE2400: ~/ece2400/tests % ./mem-leak
 Before: mem_ptr: address = 0x1a17010, value = 10
 After: mem_ptr: address = 0x7fffabe3582c, value = 20
```

Notice that there is no compilation error! And our program runs "completely
fine", or does it?

Well, if your memory leak is small enough (e.g., in this program, we lose only 4
bytes of memory), then your program may not crash. Think about if your memory
leak accumulates over time in a long program, then something nasty (e.g.,
segmentation fault) will happen! We don't want that.

So how to detect memory leak. Luckily, we have a powerful tool called "Valgrind"
to help us. Let's use the tool to run our buggy program:

```
ECE2400: ~/ece2400/tests % valgrind --leak-check=full --error-exitcode=1 ./mem-leak
==14973== Memcheck, a memory error detector
==14973== Copyright (C) 2002-2015, and GNU GPL'd, by Julian Seward et al.
==14973== Using Valgrind-3.12.0 and LibVEX; rerun with -h for copyright info
==14973== Command: ./mem-leak
==14973==
 Before: mem_ptr: address = 0x5202040, value = 10
 After: mem_ptr: address = 0xffefff4dc, value = 20
==14973==
==14973== HEAP SUMMARY:
==14973==     in use at exit: 4 bytes in 1 blocks
==14973==   total heap usage: 1 allocs, 0 frees, 4 bytes allocated
==14973==
==14973== 4 bytes in 1 blocks are definitely lost in loss record 1 of 1
==14973==    at 0x4C29B83: malloc (vg_replace_malloc.c:299)
==14973==    by 0x40047D: main (mem-leak.c:7)
==14973==
==14973== LEAK SUMMARY:
==14973==    definitely lost: 4 bytes in 1 blocks
==14973==    indirectly lost: 0 bytes in 0 blocks
==14973==      possibly lost: 0 bytes in 0 blocks
==14973==    still reachable: 0 bytes in 0 blocks
==14973==         suppressed: 0 bytes in 0 blocks
==14973==
==14973== For counts of detected and suppressed errors, rerun with: -v
==14973== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
```

Here I run _valgrind_ with two options _--leak-check=full_ that tells _valgrind_
to give details about any possible memory leak in our program and
_--error-exitcode=1_ that tells _valgrind_ to return an error code of 1 if any
memory error is detected.

Let's dive into what _valgrind_ is telling us here. We thought our program ran
fine, but Valgrind reported that we "definitely lost" 4 bytes of memory on the
heap and that no memory block on the heap was "still reachable" at the end of
the program. The report tells us exactly what we expect in the buggy program
right? Our heap memory block has no pointer pointing to it at the end of the
program, so it is not reachable. Since there is no way to reach to the block, we
could not free that block. Therefore, that block of heap memory is definitely
lost.

When you compile your program with _-g_ option, _valgrind_ can tell you where
the leak exactly is in our program. In this case, we lost 4 bytes that were
allocated in line 7 of _mem-leak.c_.

Let's try to fix the memory bug and re-run _valgrind_ on your own to verify the
leak is actually fixed.

## 2. Double free your memory

Another common problem is that a block of memory on the heap can be freed twice.
Let's look at this buggy program:

```
 1 #include <stdlib.h>
 2 #include <stdio.h>
 3
 4 void foo( int* mem_ptr ) {
 5   printf("In foo(): mem_ptr: address = %p, value = %d\n", mem_ptr, *mem_ptr);
 6   free( mem_ptr );
 7 }
 8
 9 int main( void ) {
10
11   // Allocate an int on the heap
12   int* mem_ptr = ( int* ) malloc( sizeof( int) );
13
14   // Initialize that int variable
15   *mem_ptr = 10;
16
17   printf("In main(): mem_ptr: address = %p, value = %d\n", mem_ptr, *mem_ptr);
18
19   // Call foo
20   foo( mem_ptr );
21
22   // Did foo free mem_ptr? Maybe not, so let's just free it here just in case!
23   free( mem_ptr );
24
25   return 0;
26 }
```

In line 12, we allocate a block of memory on the heap. In line 20, we pass
"mem_ptr" to _foo()_. In line 6, _foo()_ after printing the value pointed by
"mem_ptr" frees the block. In line 23, _main()_ tries to re-free "mem_ptr". You
may think that in this small program, it's easy to see that "mem_ptr" is freed
twice, right? In reality, it may be really hard to see this problem especially
when multiple pointers point to the same block of memory (i.e., this is called
pointer aliasing).

Let's run the program and see what will happen:

```
ECE2400: ~/ece2400/tests % gcc -Wall -g -O3 -o double-free double-free.c
ECE2400: ~/ece2400/tests % ./double-free
In main(): mem_ptr: address = 0xcec010, value = 10
In foo(): mem_ptr: address = 0xcec010, value = 10
*** Error in `./double-free': double free or corruption (fasttop): 0x0000000000cec010 ***
======= Backtrace: =========
/lib64/libc.so.6(+0x81429)[0x7f5564d81429]
./double-free[0x4004f8]
/lib64/libc.so.6(__libc_start_main+0xf5)[0x7f5564d223d5]
./double-free[0x400525]
======= Memory map: ========
00400000-00401000 r-xp 00000000 00:28 190587185                          /home/qtt2/ece2400/tests/double-free
00600000-00601000 r--p 00000000 00:28 190587185                          /home/qtt2/ece2400/tests/double-free
00601000-00602000 rw-p 00001000 00:28 190587185                          /home/qtt2/ece2400/tests/double-free
00cec000-00d0d000 rw-p 00000000 00:00 0                                  [heap]
7f5560000000-7f5560021000 rw-p 00000000 00:00 0
7f5560021000-7f5564000000 ---p 00000000 00:00 0
7f5564aea000-7f5564aff000 r-xp 00000000 fd:00 1188523                    /usr/lib64/libgcc_s-4.8.5-20150702.so.1
7f5564aff000-7f5564cfe000 ---p 00015000 fd:00 1188523                    /usr/lib64/libgcc_s-4.8.5-20150702.so.1
7f5564cfe000-7f5564cff000 r--p 00014000 fd:00 1188523                    /usr/lib64/libgcc_s-4.8.5-20150702.so.1
7f5564cff000-7f5564d00000 rw-p 00015000 fd:00 1188523                    /usr/lib64/libgcc_s-4.8.5-20150702.so.1
7f5564d00000-7f5564ec3000 r-xp 00000000 fd:00 148997                     /usr/lib64/libc-2.17.so
7f5564ec3000-7f55650c2000 ---p 001c3000 fd:00 148997                     /usr/lib64/libc-2.17.so
7f55650c2000-7f55650c6000 r--p 001c2000 fd:00 148997                     /usr/lib64/libc-2.17.so
7f55650c6000-7f55650c8000 rw-p 001c6000 fd:00 148997                     /usr/lib64/libc-2.17.so
7f55650c8000-7f55650cd000 rw-p 00000000 00:00 0
7f55650cd000-7f55650ef000 r-xp 00000000 fd:00 148990                     /usr/lib64/ld-2.17.so
7f55652c6000-7f55652c9000 rw-p 00000000 00:00 0
7f55652eb000-7f55652ee000 rw-p 00000000 00:00 0
7f55652ee000-7f55652ef000 r--p 00021000 fd:00 148990                     /usr/lib64/ld-2.17.so
7f55652ef000-7f55652f0000 rw-p 00022000 fd:00 148990                     /usr/lib64/ld-2.17.so
7f55652f0000-7f55652f1000 rw-p 00000000 00:00 0
7ffeec44c000-7ffeec46e000 rw-p 00000000 00:00 0                          [stack]
7ffeec485000-7ffeec487000 r-xp 00000000 00:00 0                          [vdso]
ffffffffff600000-ffffffffff601000 r-xp 00000000 00:00 0                  [vsyscall]
Aborted (core dumped)
```

Oopps, our program crashed! No worries. Our friend _valgrind_ can help us detect what went wrong. Let's run _valgrind_ and see what it reports.

```
ECE2400: ~/ece2400/tests % valgrind --leak-check=full --error-exitcode=1 ./double-free
==23220== Memcheck, a memory error detector
==23220== Copyright (C) 2002-2015, and GNU GPL'd, by Julian Seward et al.
==23220== Using Valgrind-3.12.0 and LibVEX; rerun with -h for copyright info
==23220== Command: ./double-free
==23220==
In main(): mem_ptr: address = 0x5202040, value = 10
In foo(): mem_ptr: address = 0x5202040, value = 10
==23220== Invalid free() / delete / delete[] / realloc()
==23220==    at 0x4C2AC7D: free (vg_replace_malloc.c:530)
==23220==    by 0x4004F7: main (double-free.c:23)
==23220==  Address 0x5202040 is 0 bytes inside a block of size 4 free'd
==23220==    at 0x4C2AC7D: free (vg_replace_malloc.c:530)
==23220==    by 0x4004EF: main (double-free.c:20)
==23220==  Block was alloc'd at
==23220==    at 0x4C29B83: malloc (vg_replace_malloc.c:299)
==23220==    by 0x4004CA: main (double-free.c:12)
==23220==
==23220==
==23220== HEAP SUMMARY:
==23220==     in use at exit: 0 bytes in 0 blocks
==23220==   total heap usage: 1 allocs, 2 frees, 4 bytes allocated
==23220==
==23220== All heap blocks were freed -- no leaks are possible
==23220==
==23220== For counts of detected and suppressed errors, rerun with: -v
==23220== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
```

Valgrind tells us that there was an "invalid free()" in line 23 of
_double-free.c_. Thanks to Valgrind, now you know exactly what the problem is.
Can you fix it on your own and re-run valgrind?

## 3. Invalid memory access

Remember that C compiler does not check out-of-bounds array access. You may
accidentally access some memory blocks that are not allocated. Let's consider
this buggy program:

```
 1 #include <stdlib.h>
 2 #include <stdio.h>
 3
 4 int main( void ) {
 5
 6   const int size = 10;
 7   int* mem_ptr = ( int* ) malloc( sizeof( int ) * size );
 8
 9   for ( int i = 0; i <= size; i++ ) {
10     printf( "Initializing mem_ptr[%d] ... \n", i );
11     mem_ptr[i] = i;
12   }
13
14   // Print out the array
15   for ( int i = 0; i <= size; i++ ) {
16     printf( "mem_ptr[%d] = %d\n", i, mem_ptr[i] );
17   }
18
19   // Free mem_ptr
20   free(mem_ptr);
21
22   return 0;
23 }
```

Notice that the program allocates an array of 10 "int" elements on the heap (in
line 7). It by mistake initializes the 11th element when _i == size_ in line 9.
Then in line 15, the program tries to read that unallocated element. When you compile and run the program, you will get something like this:

```
ECE2400: ~/ece2400/tests % gcc -Wall -g -O3 -o out-of-bound-access out-of-bound-access.c
ECE2400: ~/ece2400/tests % ./out-of-bound-access
Initializing mem_ptr[0] ...
Initializing mem_ptr[1] ...
Initializing mem_ptr[2] ...
Initializing mem_ptr[3] ...
Initializing mem_ptr[4] ...
Initializing mem_ptr[5] ...
Initializing mem_ptr[6] ...
Initializing mem_ptr[7] ...
Initializing mem_ptr[8] ...
Initializing mem_ptr[9] ...
Initializing mem_ptr[10] ...
mem_ptr[0] = 0
mem_ptr[1] = 1
mem_ptr[2] = 2
mem_ptr[3] = 3
mem_ptr[4] = 4
mem_ptr[5] = 5
mem_ptr[6] = 6
mem_ptr[7] = 7
mem_ptr[8] = 8
mem_ptr[9] = 9
mem_ptr[10] = 10
*** Error in `./out-of-bound-access': free(): invalid next size (fast): 0x0000000001d1e010 ***
======= Backtrace: =========
/lib64/libc.so.6(+0x81429)[0x7fdf3ec46429]
./out-of-bound-access[0x400524]
/lib64/libc.so.6(__libc_start_main+0xf5)[0x7fdf3ebe73d5]
./out-of-bound-access[0x400556]
======= Memory map: ========
00400000-00401000 r-xp 00000000 00:28 190587184                          /home/qtt2/ece2400/tests/out-of-bound-access
00600000-00601000 r--p 00000000 00:28 190587184                          /home/qtt2/ece2400/tests/out-of-bound-access
00601000-00602000 rw-p 00001000 00:28 190587184                          /home/qtt2/ece2400/tests/out-of-bound-access
01d1e000-01d3f000 rw-p 00000000 00:00 0                                  [heap]
7fdf38000000-7fdf38021000 rw-p 00000000 00:00 0
7fdf38021000-7fdf3c000000 ---p 00000000 00:00 0
7fdf3e9af000-7fdf3e9c4000 r-xp 00000000 fd:00 1188523                    /usr/lib64/libgcc_s-4.8.5-20150702.so.1
7fdf3e9c4000-7fdf3ebc3000 ---p 00015000 fd:00 1188523                    /usr/lib64/libgcc_s-4.8.5-20150702.so.1
7fdf3ebc3000-7fdf3ebc4000 r--p 00014000 fd:00 1188523                    /usr/lib64/libgcc_s-4.8.5-20150702.so.1
7fdf3ebc4000-7fdf3ebc5000 rw-p 00015000 fd:00 1188523                    /usr/lib64/libgcc_s-4.8.5-20150702.so.1
7fdf3ebc5000-7fdf3ed88000 r-xp 00000000 fd:00 148997                     /usr/lib64/libc-2.17.so
7fdf3ed88000-7fdf3ef87000 ---p 001c3000 fd:00 148997                     /usr/lib64/libc-2.17.so
7fdf3ef87000-7fdf3ef8b000 r--p 001c2000 fd:00 148997                     /usr/lib64/libc-2.17.so
7fdf3ef8b000-7fdf3ef8d000 rw-p 001c6000 fd:00 148997                     /usr/lib64/libc-2.17.so
7fdf3ef8d000-7fdf3ef92000 rw-p 00000000 00:00 0
7fdf3ef92000-7fdf3efb4000 r-xp 00000000 fd:00 148990                     /usr/lib64/ld-2.17.so
7fdf3f18b000-7fdf3f18e000 rw-p 00000000 00:00 0
7fdf3f1b0000-7fdf3f1b3000 rw-p 00000000 00:00 0
7fdf3f1b3000-7fdf3f1b4000 r--p 00021000 fd:00 148990                     /usr/lib64/ld-2.17.so
7fdf3f1b4000-7fdf3f1b5000 rw-p 00022000 fd:00 148990                     /usr/lib64/ld-2.17.so
7fdf3f1b5000-7fdf3f1b6000 rw-p 00000000 00:00 0
7ffe872bd000-7ffe872df000 rw-p 00000000 00:00 0                          [stack]
7ffe872e7000-7ffe872e9000 r-xp 00000000 00:00 0                          [vdso]
ffffffffff600000-ffffffffff601000 r-xp 00000000 00:00 0                  [vsyscall]
Aborted (core dumped)
```

The program crashed at the very end when it tried to free an unallocated memory block. Let's run it using valgrind:

```
ECE2400: ~/ece2400/tests % valgrind --leak-check=full --error-exitcode=1 ./out-of-bound-access
==31457== Memcheck, a memory error detector
==31457== Copyright (C) 2002-2015, and GNU GPL'd, by Julian Seward et al.
==31457== Using Valgrind-3.12.0 and LibVEX; rerun with -h for copyright info
==31457== Command: ./out-of-bound-access
==31457==
Initializing mem_ptr[0] ...
Initializing mem_ptr[1] ...
Initializing mem_ptr[2] ...
Initializing mem_ptr[3] ...
Initializing mem_ptr[4] ...
Initializing mem_ptr[5] ...
Initializing mem_ptr[6] ...
Initializing mem_ptr[7] ...
Initializing mem_ptr[8] ...
Initializing mem_ptr[9] ...
Initializing mem_ptr[10] ...
==31457== Invalid write of size 4
==31457==    at 0x4004E6: main (out-of-bound-access.c:11)
==31457==  Address 0x5202068 is 0 bytes after a block of size 40 alloc'd
==31457==    at 0x4C29B83: malloc (vg_replace_malloc.c:299)
==31457==    by 0x4004D1: main (out-of-bound-access.c:7)
==31457==
mem_ptr[0] = 0
mem_ptr[1] = 1
mem_ptr[2] = 2
mem_ptr[3] = 3
mem_ptr[4] = 4
mem_ptr[5] = 5
mem_ptr[6] = 6
mem_ptr[7] = 7
mem_ptr[8] = 8
mem_ptr[9] = 9
==31457== Invalid read of size 4
==31457==    at 0x400500: main (out-of-bound-access.c:16)
==31457==  Address 0x5202068 is 0 bytes after a block of size 40 alloc'd
==31457==    at 0x4C29B83: malloc (vg_replace_malloc.c:299)
==31457==    by 0x4004D1: main (out-of-bound-access.c:7)
==31457==
mem_ptr[10] = 10
==31457==
==31457== HEAP SUMMARY:
==31457==     in use at exit: 0 bytes in 0 blocks
==31457==   total heap usage: 1 allocs, 1 frees, 40 bytes allocated
==31457==
==31457== All heap blocks were freed -- no leaks are possible
==31457==
==31457== For counts of detected and suppressed errors, rerun with: -v
==31457== ERROR SUMMARY: 2 errors from 2 contexts (suppressed: 0 from 0)
```

Valgrind reported an "Invalid write of size 4" in line 11 and an "Invalid read
of size 4" in line 16. They're exactly where we by mistake accessed data blocks
outside our allocated array.

Now, you can hopefully clearly see the bug and fix it on your own.

## How to use valgrind in your PAs

In PAs, we provide you a make target called "make memcheck" that you can use to do memory check on your tests. You can do like this

```
% # go to your PA directory
% cd ${HOME}/ece2400/<netid>/pa2-dstruct
% mkdir -p build
% cd build
% # run memcheck on all tests
% make memcheck
% # run memcheck on a single test (e.g., dlist-basic-tests)
% make memcheck-dlist-basic-tests
% # see reports generated by Valgrind
% cd memtest-logs
% geany dlist-basic-tests.log &
```
