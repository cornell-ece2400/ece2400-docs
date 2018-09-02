FAQ: C Basics
==========================================================================

This is a collection of commonly asked questions on C basics.

Q: How do understand the include guard?
--------------------------------------------------------------------------
I noticed this on the sec2 handout

```
#ifndef WARM_COLORS_TXT
#define WARM_COLORS_TXT
red
orange
yellow
#endif
```

If I have already defined a macro, then excute the  #ifndef  directive,
then include the content again. How does this operation skip over the
contents of the same file?

A: How do understand the include guard?
--------------------------------------------------------------------------

From my understanding, what the #ifindef conditional does is check to see
if this macro has been defined already. If it has not then, the code below
the #ifindef line is run and the macro us defined. On the other hand, if it
already exists, meaning it has a value, these lines below #ifindef will not
be processed. So, the preprocessor determines if the macro exists before
including the leading code in the compilation process. In other words, if
something has a value during the course of the program, it is stored and so
not changed again during the rest of the compilation process.

An example I saw online that might help:

```
#include <stdio.h>

#define YEARS_OLD 12

#ifndef YEARS_OLD
#define YEARS_OLD 10
#endif

int main()
{
   printf("TechOnTheNet is over %d years old.\n", YEARS_OLD);
   return 0;
}
```

In this case, since YEARS_OLD is defined before ifndef, the code below it
will be skipped over because the macro is already defined and so the
compiler will skip over to #endif. If you run this code, you will see that
YEARS_OLD has a value of 12. If we removed the first #define statement and
just had this:

```
#ifndef YEARS_OLD
#define YEARS_OLD 10
#endif

int main()
{
   printf("TechOnTheNet is over %d years old.\n", YEARS_OLD);
   return 0;
}
```

Then the lines under #ifndef would be processed and YEARS_OLD would attain
a value of 10.

Hope this answers your question!


