
Topic 2: C Recursion
==========================================================================

This topic will discussion recursion, a powerful programming pattern that
can greatly simplify implementing some common programming problems.
Equally important, recursion will give us an opportunity to apply what we
have learned with respect to functions, conditional statements, and
iteration statements in a new context.

Put simply, _recursion_ is the process of defining a function in terms of
itself. As an analogy, assume we wish to lookup the word "programming" in
the dictionary:

 - <https://www.dictionary.com/browse/programming>

which provides this definition: "the act or process of planning or
writing a program". That is not too helpful, since if we don't know the
meaning of "programming" we likely do not know the meaning of "program".
So let's look the word "programming" up as well:

 - <https://www.dictionary.com/browse/program>

which provides eight definitions, one of which is "a precise sequence of
instructions enabling a computer to perform a task; a piece of software."
We might also want to learn the meaning of the word "computer" so we
could look up this word as well:

 - <https://www.dictionary.com/browse/computer>

And so on. Let's take step back and write some pseudo-code for an
algorithm that captures the process we used to define program:

    :::text
    lookup( term )
      foreach word in definition
        if not understand word
          lookup( word )

This is a _recursive_ algorithm since it is defined in terms of itself.
In defining `lookup` we might need to call `lookup` again one or more
times. Eventually, the algorithm terminates when we understand all of the
words in a given definition. In the rest of this topic, we will explore
how to implement C recursion.

1. Single Recursion
--------------------------------------------------------------------------

To illustrate single recursion, we will use two different examples. One
to calculate the factorial of a number, and the other to reverse the
digits in a decimal number.

### Factorial

Let's use an algorithm to compute the factorial of a number to illustrate
_single recursion_. Recall from mathematics, that the factorial of a
number ($n!$) is:

$$
n!=\begin{cases}
  \; 1               & \text{if $n = 0$} \\
  \; n \times (n-1)! & \text{if $n > 0$}
\end{cases}
$$

This is a recursive definition since defining $n!$ depends on the
definition of $(n-1)!$. Here is an example of the factorial of the
numbers 0 through 5.

$$
\begin{align}
  0! &= 1                                           \\
  1! &= 1                                           \\
  2! &= 1 \times 2 = 2                              \\
  3! &= 1 \times 2 \times 3 = 6                     \\
  4! &= 1 \times 2 \times 3 \times 4 = 24           \\
  5! &= 1 \times 2 \times 3 \times 4 \times 5 = 120
\end{align}
$$

A function to calculate the factorial of a given number using a `for`
loop is shown below.

    int factorial( int n )
    {
      int result = 1;
      for ( int i = 1; i <= n; i++ )
        result = result * i;
      return result;
    }

This is a perfectly valid iterative algorithm, but notice that it really
has very little in common wiht the original mathematical formulation. The
original mathematical formulation does not have any notion of a `result`
variale or a loop. The mathematical formalation is inherently recursive,
and so this begs the question: Can we implement factorial more directly
using recursion? A function which uses a recursive algorithm is shown
below.

    int factorial( int n )
    {
      if ( n == 0 )
        return 1;
      if ( n > 1 )
        return n * factorial( n - 1 );
    }

The conditional statement on lines 5 and 6 is called the _recursive
case_. The recursive case is where we make the recursive function call
usually with some kind of more limited version of the current function's
input parameters. In this case, we decrement the input parameter by one
before passing it into the recursive function call. In our original
analogy, this is where we lookup a new word which we do not understand in
the current definition. This is function is using _single recursion_
since it each function will at most call itself once. The conditional
statement on lines 3 and 4 is called the _base case_. The base case is
where we stop the recursion and start "unwinding" the recursive function
calls. In our original analogty, this is where we understand all of the
words in a definition).

So far, recursion can still seem a little like magic. How is really
possible for a function to be defined in terms of itself? We can use the
exact same "by-hand" execution approach we learned in the previous topic
to really make this concrete. A state diagram for calculating $3!$ is
shown below.

FIG

Notice that the recursive function callee stack frames are _nested_
within the caller stack frames. We sometimes make the vertical lines for
these nested stack frames "merge" together once we get to more than two
levels of nesting. Also notice how we must allocate some temporary
storage on the caller's stack frame for the return value of the recursive
function call. This is because we need some place to put the return value
before deallocating the callee's stack frame. Finally, notice how we
carefully use small numbers in a circle to record the location of each
function call.

Let's spend some time thinking more critically about this recursive
function. What happens if `n` is negative? The function would reach the
end of the function body without executing a return statement. This
causes undefined behavior. The function might return 0 or might return a
random value or might crash the program. The compiler may or may not give
a warning. Let's modify our function to return 1 if `n` is negative, and
to ensure it is obvious to both the compiler and the reader that this
function always reaches a `return` statement regardless of the input
value.

    int factorial( int n )
    {
      if ( n <= 0 )
        return 1;

      return n * factorial( n - 1 );
    }

DISCUSS ITERATIVE VS RECURSIVE

### Recursive Helper Functions

Assume we are given a decimal number, and we wish to produce a new
decimal number with the decimal digits in reverse order. While it is
certainly possible to solve this problem using an interative algorithm, a
recursive algorithm can also offer an elegant solution.

Let's reason through a recursive algorithm. Each recursive function call
will take a input with some number of decimal digits.

process one digit of the input starting from the least significant
to the most significant digit. The base case is when we have finished
processing all of the digits in the input. Then the crititical step is to
answer the two recursive questions:

  1. What is the base case?

      - When we have finished processing all of the digits in the input.
        In other words when the input is zero.

  1. How can we decompose a larger problem into one or more smaller problems?

      - If we have a number with $d$ digits, then we can remove the least
        significant digit and reverse the remaining $d-1$ digits. The
        larger problem is to reverse a number with $d$ digits, and the
        smaller problem is to reverse a number with $d-1$ digits.

  2. How can we solve the larger problem by combining the smaller problems?

      - Once we have reversed the remaining $d-1$ digits, we can create a
        $d$ digit number by taking the previously removed least
        significant digit and making it the new most significant digit
        along with the reversed $d-1$ digits.

Let's sketch an initial attempt at an implementation of this algorithm.

    int reverse_digits( int input )
    {
      if ( input < 10 )
        return input;

      int x = input % 10;
      int y = input / 10;
      int result = reverse_digits( y );
      // how do we combine the result and x?
      ...
    }

MAYBE we should go back to the fixed four digit version?

Unfortunately, it is not clear how to actually implement using the
smaller problem to solve the larger problem. We would need to know what
to multiply `x` by to ensure we can move it to the most significant
digit.

A recursive
function implementing this algorithm is shown below.

    int reverse_digits_h( int place, int x )
    {
      if ( place == 10 )
        return x;

      int y = x % 10;
      int z = reverse_digits_h( place/10, x/10 );
      return y*place + z;
    }

    int reverse_digits( int x )
    {
      return reverse_digits_h( 1000, x );
    }

This recursive function uses a _helper_ function. This is often necessary
when we need to pass along additional state that is not part of the
original recursive function. Here we use `place` to keep track of what
place value we are currently processing. We start by setting `place` to
be 1000 since the very first recursive function call will be putting a
new digit into the thousands place. Each recursive function call will
move the place value over one digit (i.e., divide by 10) and also move
`x` over by one digit (i.e., divid by 10). Line 6 calculates the least
significant digit, line 7 uses a recursive function call to reverse the
remaining digits (i.e., solves a smaller subproblem), and line 8 puts the
least significant digit back in the most signficant digit location.

DISCUSS ITERATIVE VS RECURSIVE

??? question "Activity 1: State Diagram for Count Digits"

    Draw a state diagram corresponding to the execution of this program.
    Remember to show the function calle stack frames as being nested.

??? question "Activity 2: Write a Recursive Function to Find a Digit"

    Use Repl.it to develop a function with the following declaration:

        int find_digit( int x, int digit )

    This function should treat `x` as a decimal number. It should compare
    each decimal digit in `x` to the given `digit` and return 1 if the
    given `digit` is found within `x`. It should return 0 otherwise.
    Ignore leading zeros. For example, if `x` is 1234 then it should
    return 1 if the given `digit` is `1`, `2`, `3`, or `4` and should
    return 0 if the given `digit` is `5`, `6`, `7`, `8`, `9`, or `0`. You
    must use a recursive algorithm. Think critically about what is the
    base case and what is the recursive case. Here is an initial Repl.it
    to get you started.

      - <https://repl.it/@cbatten/ece2400-T02-notes-activity2>

??? question "Activity 3: Write an Iterative Function to Find a Digit"

    Use Repl.it to develop a function with the same declaration as in the
    previous activity, except use an iterative instead of a recursive
    algorithm. Here is an initial Repl.it to get you started. _Hint: You
    will need to use a while loop since the number of digits in `x` is
    initially unknown._

      - <https://repl.it/@cbatten/ece2400-T02-notes-activity2>

2. Multiple Recursion
--------------------------------------------------------------------------

3. Writing a Recursive Function
--------------------------------------------------------------------------
