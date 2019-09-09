
Coding Conventions
==========================================================================

Any significant programming project will usually require developers to
use a standardized set of coding conventions. These conventions might be
set by a company, the leaders of an open-source project, or simply
through historical precedent. Standardized coding conventions enable code
written by multiple developers to be consistent and improves readability,
maintainability, and extensibility. We have developed a simple set of
coding conventions for ECE 2400 that we would like you to use in all
programming assignments. Keep in mind that these are just guidelines, and
there may be situations where it is appropriate to defy a convention if
this ultimately improves the overall code quality.

Note that some of these conventions have been adapted from the [Google
C++ Style Guide](https://google.github.io/styleguide/cppguide.html). In
general, anything not covered by the guidelines in this document should
assume the Google style guide.

1. Directories and Files
--------------------------------------------------------------------------

This section discusses the physical structure of how files should be
organized in a project.

### 1.1. Directories

All header, inline, data, and source files should be in a single `src`
directory. All tests should be a in a single `tests` directory. Anything
other than ad-hoc testing should always be done in a separate build
directory.

### 1.2. File Names

Files should be named in all lowercase and should use a dash (`-`) to
separate words. C source files should use the `.c` filename extension,
and C++ source files should use the `.cc` filename extension. Header
files should use the `.h` filename extension, and inline files should use
the `.inl` filename extension. Data files that contain C/C++ code and are
meant to be included using the C preprocessor should use the `.dat`
filename extension. All test programs should end in `-test.c`. All
evaluation programs should end in `-eval.c`.

### 1.3. Header and Inline Files

All header files should be self-contained. A header should include all
other headers it needs. The definitions for template and inline functions
should be placed in a separate `.inl` file and included at the end of the
header. Every header should use include guards where the name of the
include guard preprocessor macro is derived directly from the filename.
For example, a header file named `foo-bar.h` would use the following
include guards:

    :::c
    #ifndef FOO_BAR_H
    #define FOO_BAR_H

    #endif // FOO_BAR_H

2. Formatting
--------------------------------------------------------------------------

This section discusses general formatting that is common across all kinds
of files.

### 2.1. Line Length

Lines in all files should in general be less than 80 characters. Using
less than 74 characters is ideal since this is a natural width that
enables reasonable font sizes to be used when using side-by-side code
development with two listings on modern laptops and side-by-side code
development with three to four listings on 24" to 27" monitors. Lines
longer than 80 characters should be avoided unless there is a compelling
reason to use longer lines to increase code quality.

### 2.2. Indentation

Absolutely no tabs are allowed. Only spaces are allowed for the purposes
of indentation. The standard number of spaces per level of indentation is
two. Here is an example:

    ::::c
    int gcd( int x, int y )
    {
      while ( y != 0 ) {
        if ( x < y ) {
          int temp = x;
          y = temp;
          x = y;
        }
        else {
          x = x - y;
        }
      }
      return x;
    }

### 2.3. Vertical Whitespace

Vertical whitspace can and should be used to separate conceptually
distinct portions of your code. A blank line within a block of code
serves like a paragraph break in prose: visually separating two thoughts.
Vertical whitespace should be limited to a single blank line. Do not use
two or more blank lines in a row.

Do not include a blank line at the beginning and end of the function body
in a function definition. So this is incorrect:

    :::c
    int foo()
    {

      stmt1;
      return 0;

    }

This is correct:

    :::c
    int foo()
    {
      stmt1;
      return 0;
    }

### 2.4. Horizontal Whitespace

Absolutely no tabs are allowed. Only spaces are allowed for the purposes
of indentation. The standard number of spaces per level of indentation is
two. In general, horizontal whitespace should be used to separate
distinct conceptual "tokens". Do not cram all of the characters in an
expression together without any horizontal whitespace.

There should be white space around binary operators. Here is an example:

    :::c
    int a = b*c;   // incorrect
    int a = b * c; // correct

Use explicit parenthesis to make operator precendence explicit:

    :::c
    int a = a < 0 && b != 0;             // incorrect
    int a = ( ( a < 0 ) && ( b != 0 ) ); // correct

In some cases, we should _not_ include whitespace around an operator
because the operator is not delimiting two distinct conceptual "tokens".
Here are some examples:

    :::c
    int a = obj . field;  // incorrect
    int a = obj.field;    // correct
    int a = obj -> field; // incorrect
    int a = obj->field;   // correct
    obj . method( b );    // incorrect
    obj.method( b );      // correct
    obj -> method( b );   // incorrect
    obj->method( b );     // correct

### 2.5. Variable Declarations

There should be whitespace around the assignment operator. Here is an
example:

    :::c
    int a=3;   // incorrect
    int a = 3; // correct

If possible, consder vertically aligning the variable names and
assignment operators for related variables:

    :::c
    unsigned int a     = 32;
    int*         a_ptr = &a;

Never declare multiple variables in a single statement. Always use
multiple statements. Here is an example:

    :::c
    int a, b; // incorrect
    int a;    // correct
    int b;    // correct

### 2.6. Conditional Statements

`if` conditional statements should look like this:

    :::c
    if ( conditional_expression0 ) {
      statement0;
    }
    else if ( conditional_expression1 ) {
      statement1;
    }
    else {
      statement2;
    }

Notice the use of spaces inside the parentheses since the `()` tokens
should be conceptually separated from the conditional expression. If you
use curly braces for one part of an if/then/else statement you must use
them for all parts of the statement. Avoid single line if statements:

    :::c
    if ( conditional_expression0 ) return 1; // incorrect
    if ( conditional_expression0 )           // correct
      return 0;                              // correct

### 2.7. Iteration Statements

`for` loops should look like this:

    :::c
    for ( int i = 0; i < size; i++ ) {
      loop_body;
    }

Notice the extra horizontal whitespace used to separate the parentheses
from the initialization statement and the increment statement. The open
curly brace should be on the same line as the `for` statement.

### 2.8. Function Definitions

Function definitions should look like this:

    :::c
    int foo_bar( int a, int b )
    {
      function_body;
    }

Insert space inside the parenthesis. Notice that for functions the open
curly brace goes on its own line. Do not insert a space between the
function name and the open parenthesis. So this is incorrect:

    :::c
    // incorrect
    int foo_bar ( int a, int b )
    {
      function_body;
    }

### 2.9. Function Calls

Function calls should usually use whitespace inside the parenthesis. For
example:

    :::c
    int result = gcd( 10, 15 );

If there is a single parameter, sometimes it may be more appropriate to
eliminate the whitespace inside the parenthesis.

3. Naming
--------------------------------------------------------------------------

### 3.1. Type Names

For C programs, the names of user-defined types should usually be all
lowercase, use underscores (`_`) to separate words, and use a `_t`
suffix.

    ::c
    typedef unsigned int uint_t;

For C++ programs, the names of user-defined types should usually use
CamelCase.

    ::c++
    class FooBar
    {
      ...
    };

When specifying pointer types, the `*` should be placed with the type
without whitespace:

    :::c
    int * a_ptr; // incorrect
    int *a_ptr;  // incorrect
    int* a_ptr;  // correct

As a reminder, never declare multiple variables in a single statement.
This is never allowed:

    :::
    int *a_ptr, *b_ptr; // not allowed!

### 3.2. Variable Names

The names of variables should always be all lowercase with underscores
(`_`) to separate words. Do _not_ use CamelCase for variable names. For
pointers, use a `_ptr` or `_p` suffix. For data member fields, use a `m_`
prefix. While single letter variable names are common in the lecture
examples, single letter variable names should be very rare in real code.

### 3.3. Function/Method Names

The names of free functions and methods should always be all lowercase
with underscores (`_`) to separate words. Do _not_ use CamelCase for
function or method names.

4. Comments
--------------------------------------------------------------------------

Though a pain to write, comments are absolutely vital to keeping our code
readable. The following rules describe what you should comment and where.
But remember: while comments are very important, the best code is
self-documenting. Giving sensible names to types and variables is much
better than using obscure names that you must then explain through
comments. When writing your comments, write for your audience: the next
contributor who will need to understand your code. Be generous — the next
one may be you!

Do not state the obvious. In particular, don't literally describe what
code does, unless the behavior is nonobvious to a reader who understands
C/C++ well. Instead, provide higher level comments that describe why the
code does what it does, or make the code self describing.

### 4.1. Comment Style

Use `//` comments. These are perfectly acceptable now in C99. Do not use
the older `/* */` comments. Include a space after `//` before starting
your comment:

    :::c
    //without space, incorrect formatting
    // with space, correct formatting

### 4.2. File Comments

All files should include a "title block". This is a comment at the very
beginning of the file which gives the name of the file and a brief
description of the purpose and contents of the file. Title blocks should
use the following format:

    :::c
    //=========================================================================
    // foo-bar.h
    //=========================================================================
    // Description of the purpose and contents of this file.

The horizontal lines used in the title block should extend exactly 74
characters (i.e., two '/' characters and 72 `=` characters). You do not
need to duplicate comments between the `.h` and `.cc`. Often the header
will have a description of the interface, and the source file will
discuss the broad implementation approach.

### 4.3. Function Comments

Almost every function declaration in the header should have comments
immediately preceding it that describe what the function does and how to
use it. These comments may be omitted only if the function is simple and
obvious. These comments should be descriptive ("Opens the file") rather
than imperative ("Open the file"); the comment describes the function, it
does not tell the function what to do. In general, these comments do not
describe how the function performs its task. Instead, that should be left
to comments in the function definition.

Every function definition in the source file should have a comment like
this:

    :::C
    //------------------------------------------------------------------------
    // foo_bar()
    //------------------------------------------------------------------------
    // optional high-level discussion of implementation approach

### 4.4. Old Comments

Do not leave old comments in the source file. So you must remove comments
that were provided by the instructors.

5. Scoping
--------------------------------------------------------------------------

This section discusses use of local and global variables.

### 5.1. Local Variables

Place a function's variables in the narrowest scope possible. C99 no
longer requires all variables to be declared at the beginning of a
function, so declare functions close to where they are initialized.

### 5.2. Static and Global Variables

Do not use non-const static or global variables unless there is a very
good reason to do so. Const global variables are allowed and should
definitely be used instead of preprocessor defines.

6. C Pre-processor
--------------------------------------------------------------------------

Using the C pre-processor should be avoided. Use of the C pre-processor
should usually be limited to include guards and the `UTST` macros. When
the C pre-processor must be used, pre-processor macro names should be in
all capital letters and use an underscore (`_`) to separate words.

Do not use the C pre-processor to declare global constants. Use const
global variables instead.

7. Examples
--------------------------------------------------------------------------

Here is an example of an incorrectly formatted `for` loop:

    :::c
    for (int i = 0; i < n; i ++){
      a += c;
    }

There should be a space inside the parenthesis and no space between `i`
and `++`. There should be a space after the closing parenthesis and the
open curly brace. Here is the same code formatted correctly:

    :::c
    for ( int i = 0; i < n; i++ ) {
      a += c;
    }

Here is an example of an incorrectly formatted `if` statement:

    :::c
    if (a < 0 && b != 0){
      c = 1 / c;
    }
    else (x % 2 == 0){
      ...
    }

There should be a space inside the parenthesis and we need extra
parenthesis to make the operator precedence more explicit. We also need a
space between the closing parenthesis and the open curly brace.

    :::c
    if ( ( a < 0 ) && ( b != 0 ) ) {
      c = 1 / c;
    }
    else ( ( x % 2 ) == 0 ) {
      ...
    }

Once we have multiple levels of nested parenthesis, it might be more
readable to do something like this:

    :::c
    if ( (a < 0) && (b != 0) ) {
      c = 1 / c;
    }
    else ( (x % 2) == 0 ) {
      ...
    }

Here is an example of a poorly formatted return statement:

    :::c
    void foo()
    {
      ...
      return bar( x )
      * bar( y );
    }

Indentation should be used to make this more clear:

    :::c
    void foo()
    {
      ...
      return bar( x )
           * bar( y );
    }

This code does not include spaces around the assignment operator, and
isn't even consistent in its formatting:

    :::c
    double foo= b;
    int c =bar;
    double e=1;

This should look like this:

    :::c
    double foo = b;
    int    c   = bar;
    double e   = 1;

Notice how we lined up the variable names and the assignment operators
vertically.

Here is an example of incorrectly formatted code:

    :::c
    int gcd(int x, int y){
      while(y!=0)
        {

          if ( x < y){
            int t=x; x= temp; x =y;
          }
            else
              x = x - y;
        }


         return x;
    }

Here is an example of correctly formatted code:

    :::c
    //------------------------------------------------------------------------
    // gcd()
    //------------------------------------------------------------------------

    int gcd( int x, int y )
    {
      // iterate until GCD is found
      while ( y != 0 ) {

        if ( x < y ) {
          // swap x and y
          int temp = x;
          y = temp;
          x = y;
        }
        else {
          x = x - y;
        }

      }
      return x;
    }


