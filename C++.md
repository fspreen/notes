# C++ #
## References ##
A reference is a type of variable standing between a direct value and a pointer.
Effectively, a reference acts like a const pointer that is implicitly
dereferenced when accessed.  (Note the difference between _const pointer_ and
_pointer to const_, although references can also refer to const values.)
```
int value = 5;
int *const ptr = &value;
int &ref = value;
// now *ptr == 5 and ref == 5

// const reference, i.e. reference to a const
const int value2 = 6;
const &ref = value2;
```
Because references are const, they must be initialized with a valid, non-null
object.  However, a reference can be invalidated if it points to a dynamic
memory object that is moved or destroyed.

References are excellent for use in function parameters as an alternative to
pointers for passing by reference.

## Classes ##
### Prototypes ###
If necessary, a class can be prototyped similar to function prototypes.
However, the process is much simpler for classes:
```
class Point3D;
```

### Sample Classes ###
sample1.cpp
```
class Example1
{
// member vars are private by default, but this is an example
private:
    int x;
    int y;
    int z;
    static int static_val;              // MUST initialize outside of class
    static const int days_in_week = 7;  // MUST initialize here
public:
    int q;
    // C++11:  default value; overridden by any relevant constructors
    double cpp11only {1.0};

public:
    // constructor; note lack of return, default function values
    Example1(int x=1, int y=1, int z=1)
    {
        this->x = x;
        this->y = y;
        this->z = z;
    }

    // constructor; direct initialization of member values
    Example1(int a, int b, int c) : x(a), y(b), z(c)
    {
        // deliberately empty
    }

    // destructor; note lack of parameters and return
    ~Example1()
    {
        // must return dynamically allocated memory here
        // release other resources (e.g. database locks)
        // exit() will bypass this when terminating a process
    }
};
// class definition MUST end with semicolon

// Static variable initialized outside the class
Example1::static_val = 5;
```

sample2.h
```
#IFNDEF SAMPLE2_H
#DEFINE SAMPLE2_H
class Example2
{
private:
    int x;
    int y;
    int z;
public:
    int q;

public:
    Example2(int x, int y, int z);
private:
    void comp_q();
};
#ENDIF
```

sample2.cpp
```
#include "sample2.h"

Example2::Example2(int x, int y, int z)
{
    this->x = x;
    this->y = y;
    this->z = z;
}

void Example2::comp_q()
{
    q = x + y + z;
}
```

### Constructors ###
Constructors are subject to function overloading rules, similar to basic
functions.  Remember this when supplying default values.

Constructors do not have a return type.

#### No Constructor ####
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

#### Basic Constructors ####
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

#### Copy Constructors ####
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

#### Converting Constructors ####
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

#### Delegating Constructors (C++11) ####
Starting with C++11, constructors can call other constructors.  An example is a
constructor with a few parameters calling a constructor with more parameters:
```
Employee::Employee(int id, std::string name) : m_id(id), m_name(name) {}
Employee::Employee() : Employee(0, "") {}
Employee::Employee(int id) : Employee(id, "") {}
Employee::Employee(std::string name) : Employee(0, name) {}
```

#### Member Initializer Lists ####
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

* [ ] Can a member initialization list work when the parameter name is the same
      as the member variable name?

### Friend Functions ###
A friend function can access private variables and methods as if it were part of
the class.  They must be white-listed in the class definition:
```
friend void someFunction(Point3D &point);
```

Note that a function can be a friend of more than one class, as long as all
classes list the function as a friend.  Since a friend function will need to
have parameters of each class type, and one class will be defined before
another, it is necessary to have a class prototype (as above).

Class methods and entire classes can also be friends:
```
friend class MathSuite;
friend Vector3D::normalize(Point3D &point);
```

However, prototypes are needed so that the compiler doesn't encounter an
unexpected symbol.  The best recommendation is to put each class declaration in
a separate header file and function bodies in source files.

### Const Objects ###
Const objects cannot modify member variables after being constructed.  Const
objects can call only const member functions.

Const member functions will not/cannot change member variables.  (A compiler
error will result otherwise.)  The `const` is considered part of the function's
signature for overloading purposes.
```
int getValue() const;
int getValue() const { return this-> value; }
```
Const references treat an object as a const object, similar to above.  Often
seen as function parameters.
```
void printDate(const Date &date);
```

### Static Members ###
Static member variables are independent of instantiated objects.  Can be
accessed from an object or direct from the class name:
```
MathSuite math1;
math1.pi;
MathSuite::pi;
```
Static member variables _must_ be initialized in the global scope (i.e. outside
of the class definition).  Static const member variables _must_ be initialized
at the point of declaration, i.e. within the class definition.

Static methods can be defined like regular methods.  Do not have a `this`
pointer and can access only static member variables or static functions.

* [ ] Can a static method access private member variables of an object it
      creates if the object is the same type as the class?

### Operator Overloading ###
Rules of thumb for working with operator overloading functions.  Adapted from
[learncpp.com](http://www.learncpp.com) section 9.4 "Overloading operators using
member functions":

* If overloading assignment, subscript, function call, or member selection (=,
  [], (), or ->) do it with a member function.  (Required by the language.)
* Overload unary operators via member functions.  (Makes sense; there are no
  other parameters.)
* Use member functions for operators that modify the left operand.
* Use normal or friend functions for operators that don't modify the left
  operand.
* Must use normal or friend functions that take a different type as the left
  operand (e.g. operator<< being used with std::cout mechanism)

#### Increment/Decrement ####
Prefix and postfix operator overloads can be distinguished by the presence of a
dummy integer parameter:
```
Example& Example::operator++();     // prefix
Example Example::operator++(int);   // postfix
```
Because of the way postfix operators work, it needs to return a copy of the
object as it was _before_ the operation is carried out.  This requires creating
a new object and returning it by value.  (Reference won't work because it would
be destroyed as soon as the functions ends.)

#### Subscript ####
Overload the [] operator with a function like:
```
int& IntList operator[] const int index);   // for non-const objects, and assignment
const int& IntList operator[](const int index) const;   // for const objects; read-only
```
Return a reference so that values can be assigned to a selected element.  Don't
use [] on a pointer to an object, because it will act like an array of objects.
Dereference first.  Since deref has lower precedence, use parens like
`(*list)[2]`.

Note that indexes don't need to be integers; consider the possibility of
std::string.

Keeping references to returned elements doesn't work if the object moves them
around in memory, e.g. when copying for a resize.

#### Parenthesis ####
Allows for more "freeform" definition of parameter count and types.  Called when
the object is called like a function (syntactically speaking):
```
Matrix matrix;
matrix(1,2) = 4.5;  // should return double& in this instance
```
Useful for a "functor" or "function object", which allows for behavior like a
function with persistent internal state.  (Compare closures, but functors do not
capture local variables.)  Different from function+static var in that each
object has its own independent state.

Functors are useful for callbacks.

#### Typecasts ####
Kinda weird that this is possible, actually.  Allows the class to control how it
is typecast to another type.  Not limited to built-in types!  Example for
casting to an int:
```
operator int() {...}
```
Note lack of parameters and return type; assumption is that return value is of
the correct type.

#### Assignment Operator ####
Similar to the copy constructor.  The copy constructor is called when a new
object is needed for copying; the assignment operator is called if copying can
take place without constructing a new object.

By default C++ provides a basic copy constructor that does memberwise copying
(similar to the default copy constructor).  This is effectively shallow copying
and the two objects will point to the same memory block in the case of
dynamically allocated member variables.

In the case of self-assignment, return a reference to the original.  Otherwise,
it is possible to run into issues when "copying" a dynamically allocated block
of memory (e.g. if deleting the destination and then copying from the source).
```
Point3D& Point3D::operator= (const Point3D &source)
{
    if (this == &source) return *this;
    
    // ...
}
```
