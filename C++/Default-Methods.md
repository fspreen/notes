# Default Methods #
By default, the compiler will add certain methods to classes if equivalent
methods are not already defined.  It is important to be aware of these if they
do not already exist, especially if the class must manage pointers.

## Constructor ##
The term "default" constructor refers to constructors with no parameters, even
if they are defined in the code.  An implicit default constructor is created if
no other constructors exist, or if a zero-parameter constructor is created using
the `default` keyword added in C++11:
```
ClassName() = default;
```

It does not explicitly initialize any class members, acting as an empty
function.  However, it will call the default constructor of the base class, and
of non-static class members.

## Destructor ##
The default destructor is declared as if it were `inline public` and defined
with an empty function body.  This can cause issues if the class has e.g.
pointers to dynamic memory.  If the class is managing the memory, and the
pointers are not deallocated, object creation and destruction can cause memory
leaks.

## Copy Constructor ##
The default copy constructor is declared `inline public` and takes a reference
of the same type as the class.  This reference may be `const` under certain
circumstances.

Members are copied using direct initialization.  Note that this means that two
objects may exist with pointers to the same dynamic memory.

According to cppreference.com:

> The generation of the implicitly-defined copy constructor is deprecated if T
> has a user-defined destructor or user-defined copy assignment operator.
> (since C++11)

## Copy Assignment Operator ##
The default copy assignment operator is declared `inline public` and takes a
reference of the same type as the class.  This reference may be `const` under
certain circumstances.

Members are copied using member-wise copy assignment:  built-in assignment for
basic data types and copy assignment operators for class types.  Note that this
means that two objects may exist with pointers to the same dynamic memory.

## Move Constructor ##
Added in C++11.

* [ ] Learn what this is used for.

## Move Assignment Operator ##
Added in C++11.

* [ ] Learn what this is used for.
