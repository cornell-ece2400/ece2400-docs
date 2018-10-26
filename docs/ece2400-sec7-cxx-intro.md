Section 7: Introduction to C++
==============================

In this discussion, we will transition from C to C++ by incrementally
moving a simple C structure into C++. We will work on the following
concepts in this section:

- C++ Structure and Class
- Static member functions in C++ structure
- Non-static member functions in C++ structure/class

Before you start, let's source our setup script like this:

```
% source setup-ece2400.sh
```

Now clone the github repo for this discussion section. No need to fork the
repo, just clone it.

```
% mkdir -p ${HOME}/ece2400
% cd ${HOME}/ece2400
% git clone git@github.com:cornell-ece2400/ece2400-sec7-cxx-intro.git sec7
% cd sec7
% ls
```

The repo includes the following files:

- `c-version/` directory
  + `c-version/complex.c`: a C implementation of `complex_t` structure
- `cxx-versions/` directory
  + `cxx-versions/complex-v1.cc`: version 1 of our C++ implementation of
  `Complex` structure. This version introduces C++ structure and static
  member functions
  + `cxx-versions/complex-v2.cc`: version 2 of our C++ implementation of
  `Complex` structure. This version introduces non-static member functions
  for a structure in C++ and how to call them.
  + `cxx-versions/complex-v3.cc`: version 3 of our C++ implementation of
  `Complex` structure. This version introduces how to define and use
  constructors for a structure in C++. Students will also practice how to
  pass a variable to a function by reference.
  + `cxx-versions/complex-v4.cc`: version 4 of our C++ implementation of
  `Complex` structure. This version introduces copy constructor and
  operator overloading.

---

**1. Step 1: C version**

- Understand the implementation of `complex_t` structure in `c-version/complex.c`
- Compile and run the program like this

```
cd c-version/
gcc -Wall -o complex complex.c
./complex
```

**2. Step 2: First C++ version of `complex_t`**

- In this version, we're making the first steps toward C++.

- First, we change how to declare a structure. We use C++ coding convention
  to name our structure `Complex` instead of `complex_t` in C.

- Second, we move `add` and `print` functions inside the definition of
  `Complex`, and make the functions `static`.

- Third, in `main`, we call the two functions using a namespace `Complex`

**Your task**: Implement the `add` function

**3. Step 3: Second C++ version**

- In this version, we make both `add` and `print` functions non-static.

- In `main`, we change how we call the two functions. Now every call to
  `add` and `print` is associated with a specific instance of `Complex`.

- Also you may notice that, the `this_` variables in `complex-v1.cc` are
  replaced with C++ keyword `this`. In C++, `this` is a pointer to the
  current instance of a structure or class.

**Your task**: Implement the `add` function

**4. Step 4: Third C++ version**

- In this version, we introduce a constructor for our `Complex` structure.
  The constructor helps us initialize member fields of the structure.

- In `main`, we use the constructor to initialize a structure instance
  instead of initializing each member field directly.

**Your task**: Implement the `add` function. The function now takes a
reference to a structure instance instead of a pointer.

**5. Step 5: Fourth C++ version**

**Your tasks**

- First, you make a copy constructor that takes a constant reference to an
  instance of `Complex` and initialize all member fields by copying `real`
  and `imag` from the input `x` object.
- Second, you overload the `operator+` so that we can add two `Complex`
  instances together.
