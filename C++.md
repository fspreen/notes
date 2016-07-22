# C++ #
## Classes ##
### Sample Class ###
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
