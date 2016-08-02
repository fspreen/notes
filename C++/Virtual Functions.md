# Virtual Functions #
A virtual function is one that can be completely overridden in derived classes.
Normally, a function with the same prototype will only be called when invoked on
a derived object.  A pointer with a base class type will still call the base
function.

With a virtual function, a pointer to a base object can still call the derived
function.  The base object needs to declare the original function with the
keyword `virtual` like so:
```
virtual int calcNumbers(int x, int y);
```

Nothing special is needed in the derived class, but modifying the function to be
`virtual` there doesn't hurt, either.  (And any potential classes inheriting
from the derived class can also make use of the chain.)

## Pure Virtual Function ##
Instead of defining a function body, assign the function the value 0:
```
virtual int getValue() = 0;
```

A pure virtual function _must_ be overridden by child classes.  Furthermore, the
base class becomes an abstract base class and cannot be directly instantiated.
(If a derived class does not override the pure virtual function, it becomes an
abstract class in turn.)

## Interfaces ##
An interface is a class composed wholly of pure virtual functions and no member
variables.  C++ has no special syntax for interfaces, unlike other languages
such as Java.  Interfaces are applied to classes via multiple inheritance.

## Return Types ##
The method overriding a virtual function must return the original return type.
However, if the return type is a pointer or reference to a class, the overriding
function can return a pointer or reference to a derived class.  This is called
"covariant return types".  Some older compilers may not support this, though it
is not a recent part of the standard (i.e. predates C++11).

## Destructors ##
A derived destructor always overrides a virtual base class destructor.  This is
necessary to delete dynamically allocated member variables even when using a
base class pointer or reference.

Note that the destructor of the base class will still be called when the derived
class constructor is finished.

If a class has (or inherits) at least one virtual function and the destructor is
_not_ virtual, deleting it is undefined behavior!  This is true even if there
are no resources that need a destructor.

* [ ] Why is this the case?

Per the [C++ Core Guidelines](https://github.com/isocpp/CppCoreGuidelines/blob/master/CppCoreGuidelines.md#discussion-make-base-class-destructors-public-and-virtual-or-protected-and-nonvirtual),
destructors should always be public and virtual, or protected and non-virtual.

## Virtual Assignment Operators ##
Don't.  Apparently it's more trouble than it's worth.

## Suppressing a Virtual Function Call ##
Not all that useful, but good to know:
```
Base &rBase = cDerived;
rBase.Base::BaseMethod();
```

## How it Works ##
The compiler automatically adds a pointer to each appropriate class.  The
pointer points to a "virtual table" or "dispatch table" that holds pointers to
the appropriate function.

When a derived object is instantiated, it includes a pointer to its class'
virtual table of functions.  Invoking a virtual function on a base class checks
against this virtual table to find the address of the actual function in memory.
By virtue of how the table is managed, this will always be the function from the
object's original class.

The trade-off is slightly larger objects (by one pointer size) and slightly
longer runtime due to the function pointer redirection.  On modern systems, this
cost is negligible.
