# Inheritance #
Parent classes are specified in the definition of a class structure:
```cpp
class Derived : public Base
{
    // ...
};
class Derived : public Base1, public Base2
{
    // ...
};
```

Inheritance can be modified by an access specifier, as seen above.  Depending on
the combination of base class access specifier and inheritance access specifier,
a member or method can be accessed by the derived class or the "general public".

| Base class access | Public Inherit. | Protected Inherit. | Private Inherit. |
|-------------------|-----------------|--------------------|------------------|
| public            | public          | protected          | private          |
| protected         | protected       | protected          | private          |
| private           | private         | private            | private          |

A public or protected member of a base class can always be accessed by a derived
class.  Only a publicly-inherited public member can be accessed outside of a
class.

Construction/initialization happens first with the base class, followed by the
derived class.  If not specified, the default constructor of the base class is
called (i.e. the constructor with zero mandatory parameters).  It is possible to
call a different base class constructor similar to member initialization lists:
```cpp
Derived(int a, int b) : Base(a), b(b) { }
```

Destructors are called first for the derived class, then for the base class.

## References and Pointers ##
A reference or pointer can have the type of a base class but point to a derived
object.  However, the reference/pointer can use only those methods or variables
which are available to the base class.  One exception to this is
[virtual functions](Virtual Functions.md).

## Multiple Inheritance ##
Multiple base classes can be specified, as above.  If a method call or variable
is ambiguous between more than one base class, it _must_ be specified with the
base class name and the scope resolution operator:
```cpp
int main()
{
    Derived item;
    item.Base2::getName();

    return 0;
}
```
