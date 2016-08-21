# Objects and Classes in C++ #
## Prototypes ##
If necessary, a class can be prototyped similar to function prototypes.
However, the process is much simpler for classes:
```
class Point3D;
```

## Friend Functions ##
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

## Const Objects ##
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

### Mutable ###
A member variable can be declared mutable, meaning that a const method can still
modify it.  (This is good for caching, mutexes, and other uses.)  It works even
if the object was originally declared const.
```
mutable bool cache_valid;
```

## Static Members ##
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
