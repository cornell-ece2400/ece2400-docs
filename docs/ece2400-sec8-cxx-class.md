Section 8: C++ Class
====================

Before you start, let's source our setup script like this:

```
% source setup-ece2400.sh
```

Now clone the github repo for this discussion section. No need to fork the
repo, just clone it.

```
% mkdir -p ${HOME}/ece2400
% cd ${HOME}/ece2400
% git clone git@github.com:cornell-ece2400/ece2400-sec8-cxx-class.git sec8
% cd sec8
% ls
```

**1. Part 1: Organize your code in C++**

- So far in lectures, we have seen C++ code written all together in one
  single `.cc` file. That's not the case in practice when you need to deal
  with large and complex code base. Let's consider an example of the
  `Complex` class in our discussion section last week.

```
#include <cstdio>

//========================================================================
// Complex
//========================================================================
// Declaration and definition of Complex class and its members

class Complex
{
  public:
   //----------------------------------------------------------------------
   // constructors
   //----------------------------------------------------------------------

   Complex( double real_, double imag_ )
   {
     real = real_;
     imag = imag_;
   }

   Complex( const Complex& x )
   {
     real = x.real;
     imag = x.imag;
   }

   //----------------------------------------------------------------------
   // add
   //----------------------------------------------------------------------
   // Add "x" complex number to "this" complex number

   void add( const Complex& x )
   {
     real += x.real;
     imag += x.imag;
   }

   //------------------------------------------------------------------------
   // print
   //------------------------------------------------------------------------
   // Print "this" complex number

   void print()
   {
     std::printf("%.2f+%.2fi", real, imag );
   }

  private:
   double real;
   double imag;
 };

 //========================================================================
 // operator+ overload
 //========================================================================

 Complex operator+( const Complex& x, const Complex& y )
 {
   Complex tmp = x;
   tmp.add( y );
   return tmp;
 }

//========================================================================
// main
//========================================================================

int main( void )
{
  // Create complex number "a" by calling the constructor of Complex

  Complex a( 1.5, 2.5 );
  std::printf("a = ");
  a.print();
  printf("\n");

  // Create complex number "b" by calling the constructor of Complex

  Complex b ( 3.5, 4.5 );
  std::printf("b = ");
  b.print();
  printf("\n");

  // Add "a" and "b" together and store the sum to "c"

  std::printf("Doing c = a + b ...\n");
  Complex c = a + b;

  // Print out "c"

  std::printf("c = ");
  c.print();
  printf("\n");

  // Add "b" to "a"

  std::printf("Doing a += b ...\n");
  a.add( b );

  // Print out "a"

  std::printf("a = ");
  a.print();
  printf("\n");

  return 0;
}
```

- Here we declare and define `Complex` class and its members (i.e., member
  functions and member fields) all together in one `.cc` file. We also have
  a `main` function that uses the class. Let's imagine that your class has
  tens of member functions, each of which takes many lines of code to
  implement. How would your source file look like? **Gigantic!**

- Following are some drawbacks of this monolithic approach:
  + Very large code base -> very hard to maintain and debug
  + Declarations and definitions are put together -> very hard to see the
  interface of a class
  + Hard to include a class into another file or project

- Instead, we should break the code into multiple files
  + `complex.h`: contains only declarations of related classes, functions,
  and variables and no implementation detail.
  + `complex.cc`: contains only definition/implementation of classes,
  functions, and variables declared in the header file `complex.h`. This
  file needs to include the header file.
  + `complex-main.cc`: contains only user code that uses the class. This
  file needs to include the header file.

- **Your tasks**
  + Go to `part1/` directory
  + We already provide you the header file `complex.h`
  + Copy the implementation of `Complex` class's functions and the
  overloaded `operator++` into `complex.cc`. We already give you an example
  of the class's default constructor.
  + Copy the `main` function into `complex-main.cc`
  + Compile your code using `g++`

**2. Part 2: Make your RVector class**

- In this part, we will implement an `RVector` data structure in C++.
  `RVector` works exactly like the `rvector_int_t` that you implemented in
  PA 2. You will need to implement the following functions in
  `part2/rvector.cc`

  + `RVector::RVector()` - default constructor: initialize `m_max_size`,
  `m_size`, and `m_arr` to default values. Remember that the `RVector` is
  empty initially.

  + `RVector::RVector( size_t max_size )` - a constructor: initialize
  `m_max_size` to `max_size`, `m_size` and `m_arr` to default values.
  Remember that the `RVector` is empty initially.

  + `RVector::RVector( const RVector& x )` - a copy constructor: copy all
  numbers from vector `x` to this vector. Remember to allocate memory
  properly for this constructor.

  + `RVector::~RVector()` - a destructor: deallocate any dynamically
  allocated memory associated with this `RVector`.

  + `void RVector::push_back( int num )`: push back a new number into this
  vector

  + `int RVector::at( size_t index )`: return a number at a given index.
  **Throw an OutOfRangeException when the index is out of bound**

- After you finish implementing all functions, you will need to write a
  small ad-hoc tests in `part2/rvector-main.cc` to test them. Compile and
  run the test using `g++`.
