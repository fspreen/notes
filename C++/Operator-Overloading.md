# Operator Overloading #
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

## Increment/Decrement ##
Prefix and postfix operator overloads can be distinguished by the presence of a
dummy integer parameter:
```cpp
Example& Example::operator++();     // prefix
Example Example::operator++(int);   // postfix
```
Because of the way postfix operators work, it needs to return a copy of the
object as it was _before_ the operation is carried out.  This requires creating
a new object and returning it by value.  (Reference won't work because it would
be destroyed as soon as the functions ends.)

## Subscript ##
Overload the [] operator with a function like:
```cpp
int& IntList operator[](const int index);   // for non-const objects, and assignment
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

## Parenthesis ##
Allows for more "freeform" definition of parameter count and types.  Called when
the object is called like a function (syntactically speaking):
```cpp
Matrix matrix;
matrix(1,2) = 4.5;  // should return double& in this instance
```
Useful for a "functor" or "function object", which allows for behavior like a
function with persistent internal state.  (Compare closures, but functors do not
capture local variables.)  Different from function+static var in that each
object has its own independent state.

Functors are useful for callbacks.

## Typecasts ##
Kinda weird that this is possible, actually.  Allows the class to control how it
is typecast to another type.  Not limited to built-in types!  Example for
casting to an int:
```cpp
operator int() {...}
```
Note lack of parameters and return type; assumption is that return value is of
the correct type.

## Assignment Operator ##
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
```cpp
Point3D& Point3D::operator= (const Point3D &source)
{
    if (this == &source) return *this;

    // ...
}
```
