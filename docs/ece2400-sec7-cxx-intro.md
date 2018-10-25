Section 7: Introduction to C++
==============================

In this discussion, we will practice the following C++ concepts:

- Class
- Reference
- Function/Operator overloading

We will develop a class called **Vector** to represent Euclidean vector. Our
**Vector** class will be based on the **Point** class shown in the lecture.

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
% git clone git@github.com:cornell-ece2400/ece2400-sec7-cxx-intro.git sec7
% cd sec7
% ls
```

The repo includes the following files:

- CMake configuration script
  + `CMakeLists.txt`

- Source and header files
  + `src/point.h`
  + `src/point.cc`
  + `src/vector.h`
  + `src/vector.cc`

- Directed test file
  + `tests/vector-directed-tests.cc`

---

**1. Euclidean vector**

This is a definition of Euclidean vector: "_In mathematics, physics, and
engineering, a Euclidean vector (sometimes called a geometric or spatial vector,
or—as here—simply a vector) is a geometric object that has magnitude (or length)
and direction._" (from Wikipedia)

A vector has an initial point and a terminal point. A vector is called "free
vector" if its initial point is (0, 0). In this discussion, we only work with
"free vector". A free vector can be represented using x and y coordinate of its
terminal point. Following is an example of how vector _OA_ is represented in
Cartesian coordinate system:

![](img/ece2400-sec7-Cxx-intro-f9d579e6.svg)

Notice that its terminal point is point A whose x-coordinate is 2 and
y-coordinate is 3. Vector OA can be represented as OA(2, 3).

There're several operations that can be applied to vectors. Let's say we have
two vectors A(x_a, y_a) and B(x_b, y_b).

- Get the length of a vector
  + `len( A ) = sqrt( x_a * x_a + y_a * y_a )`
- Add two vectors together
  + `A + B = ( x_a + x_b, y_a + y_b )`
- Multiply a vector with a scalar value
  + `A * m = ( x_a * m, y_a *m )`
- Compare two vectors in both magnitude and direction
  + `A = B if x_a = x_b and y_a = y_b`

**2. Implement Vector class**

The definition of `Vector` class is given in `src/vector.h`. The class has a
private member called `m_terminal_point` whose type is `Point`. `Point` class is
already implemented in `src/point.h` and `src/point.c`. You may want to look at
how the interface of `Point` class looks like to use it in your `Vector` class.

You will need implement the following functions in `Vector` class.

- `get_x`: return the x-coordinate of the terminal point of a vector
- `get_y`: return the y-coordinate of the terminal point of a vector
- `len`: return the length of a vector
- `multiply`: multiply a vector with a scalar value
- `add`: add a vector with another vector
- `eq`: compare a vector with another vector in both magnitude and direction

**3. Overload some operators**

After you finish implementing all functions in `Vector` class, you will need to
overload the following operators.

- `operator+`: add two vectors together and return the sum vector
- `operator*`: multiply a vector with a scalar value and return the result
vector
- `operator==`: compare two vectors and return true if both are equal

**4. Test your implementation**

After you finish your implementation, you will need to write some directed tests
in `tests/vector-directed-tests.cc` to verify whether your `Vector` works
correctly.
