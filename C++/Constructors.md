# Constructors #
Constructors are subject to function overloading rules, similar to basic
functions.  Remember this when supplying default values.

Constructors do not have a return type.

## No Constructor ##
If all member variables are public, an initialization list can be used, similar
to a struct variable:
```
Point3D p1 = {4, 5, 6};     // initialization list
Point3d p2 {4, 5, 6};       // C++11:  uniform initialization
```

If variables are private or not initialized as above, the object will contain
uninitialized values by default.  In effect, C++ creates an empty, no-op
function as a constructor.

If _any_ constructor exists, then the default constructor is not added.

## Basic Constructors ##
A constructor with zero parameters is a default constructor:
```
Point3D::Point3D () {...}
```

A constructor with one or more parameters is a basic constructor and can be used
for direct and uniform initialization:
```
// Note the default value for z
Point3D::Point3D(int x, int y, int z=0)
{
    this->x = x;
    this->y = y;
    this->z = z;
}

Point3D p1(4, 5, 6);
Point3D p2 {4, 5, 6};   // C++11:  calls the constructor
Point3D p3(8, 9);       // calls constructor with default value for z
```

## Copy Constructors ##
Used to create a new copy of an existing object (of the same type).  Can be made
private to prevent copying.

Called any time copy initialization is used.  By default this includes
assignment (but compare assignment operator overloading).  Additionally includes
function parameters or returns being passed by value.

May or may not be called if the compiler optimizes an expression.
```
Point3D::Point3D(const Point3D &original)
{
    this->x = original.x;
    this->y = original.y;
    this->z = original.z;
}
```

The default copy constructor provided by C++ performs memberwise initialization,
i.e. copies values directly from one object to another.  If there is dynamically
allocated memory, the pointers of the copy will point to the same memory as the
original.

## Converting Constructors ##
A constructor which takes one parameter will be used for implicit conversion to
that class type.

Add the `explicit` keyword to prevent implicit conversions with that
constructor:
```
explicit Fraction(int numerator, int denominator=1) {}
```

Explicit conversions are still allowed, i.e. with direct or uniform
initialization, or explicit casting.  This can be solved by making the
constructor private similar to the copy constructor.

In C++11, the `delete` keyword can be used to completely block the function.
Also works with copy constructor and overloaded operators:
```
Fraction(char) = delete;    // any use of this constructor is an error
```

## Delegating Constructors (C++11) ##
Starting with C++11, constructors can call other constructors.  An example is a
constructor with a few parameters calling a constructor with more parameters:
```
Employee::Employee(int id, std::string name) : m_id(id), m_name(name) {}
Employee::Employee() : Employee(0, "") {}
Employee::Employee(int id) : Employee(id, "") {}
Employee::Employee(std::string name) : Employee(0, name) {}
```

## Member Initializer Lists ##
Member variables can be initialized by the constructor with special syntax
provided by C++.
```
Point3D::Point3D() : x(1), y(2), z(3) {}
Point3D::Point3D(int a, int b, int c) : x(a), y(b), z(c) {}
ArrayObject::ArrayObject() : m_array {} {}   // zero the array

// C++11:  uniform initialization
Point3D::Point3D(int a, int b, int c) : x {a}, y{b}, z{c} {}
ArrayObject::ArrayObject() : m_array {1, 2, 3, 4, 5} {}
```

It is possible for member initialization lists to have the same name for
parameters and member variables.  If a class variable is desired, use the `this`
pointer instead (like `a(this->z)` or similar).

Source variable/parameter names are evaluated in the scope of the constructor.
