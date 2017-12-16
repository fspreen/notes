# Exceptions #
C++ has exceptions for error handling, similar to Java.  The basic syntax:
```cpp
try
{
    throw -1;
}
catch(int e)
{
    std::cerr << "Error value: " << e << std::endl;
}
catch(...)
{
    std::cerr << "Some error occurred." << std::endl;
}
```

Throwing an exception immediately stops execution at that statement and proceeds
to the nearest applicable `catch` block.

Any sort of object or variable can be thrown, though it's usually best to throw
some sort of object derived from `std::exception`.  (Throwing a signed integer
isn't much different than C's `errno` error-handling.)  For complex data types,
it is best to catch exceptions by reference rather than value to avoid expensive
copy operations during error handling.

Exceptions may be thrown by object constructors to indicate that object creation
failed.  Exceptions should _never_ be thrown by destructors because destructors
may be called during the stack unwinding process.  Throwing an exception when an
exception is already being handled is ambiguous behavior and will usually cause
immediate program termination.

## Finding a Handler ##
An exception proceeds up the call stack until it finds the first matching
`catch` block.  The type of the exception must match the type specified in the
`catch` block.  However, derived classes can match base classes.  Therefore it
is best to always list more specific `catch` conditions before the general ones.

While a type may be specified, no variable is necessary unless information is
needed from the thrown object.

The catch-all handler matches everything.  Its syntax is seen in the above
example.  The `catch` statement with three dots matches any thrown object and
allows for "all else fails" type scenarios to be handled.  The example again:
```cpp
catch(...)
{
    std::cerr << "Some error occurred." << std::endl;
}
```

## std::exception ##
Unlike Java's `Throwable` interface, C++'s `std::exception` provides only one
method:  `virtual const char* what() const;` which returns a null-terminated
string explaining what happened.  There is no stack trace information available.

(Correction:  there is also a copy operator.)

## Exception Specifiers ##
This is considered a failed feature in C++ and may not be supported (or
improperly supported) by compilers.  It may also cause issues with template
functions.  I'm including it here because it may appear in other code, but
don't use it.

A function prototype or definition may specify if a function throws certain
exceptions.  Examples:
```cpp
int something() throw();            // does not throw exceptions
int something() throw(double);      // may throw a double
int something() throw(...);         // may throw anything
```
