# Templates #
Templates allow the creation of functions and classes without having to specify
certain type values.  This allows code to be written once that can be used with
many different types.  (An example is a function that adds two numerics:
integers, floating points, etc. or container classes.)
```
template <typename T>
T max(T x, T y)
{
    return (x > y) ? x : y;
}
```

The `template` keyword applies only to the next language structure.
(Technically, the function, class, etc is preceded by the `template` line, all
as one statement.)

Multiple types can be specified like `template <typename T1, typename T2>`.

## How it Works ##
At compile time, the compiler uses the template to produce many copies of the
template code by "filling in the blanks" with the types it needs.

If the template code uses operators on the template types, these operators need
to be defined for any classes that make use of the template code.  Otherwise a
compile-time error will result.

## Classes ##
The `template` modifier can precede a class definition.  If any functions are
defined outside the class definition, they will need their own `template`
modifier.  However, this will cause issues if the class definition and the
function definitions are in separate files.

The easiest solution is to place the class function definitions in the header
file as well, although this may cause some bloat.  However, the alternative is
to either `#include` the .cpp file as well, or to `#include` both header and
.cpp file in a third file that manually instantiates each needed template.

Specific instances of template classes can be explicitly forced:
```
template class SomeClass<int>;
```

### Expression Parameters ###
Not available for functions, only classes.  Allows for substitution of a value,
rather than a type.  Example:
```
template <typename T, int nSize>
class Buffer
{
private:
    T m_atBuffer[nSize];
    // ...
};

int main()
{
    Buffer<int, 12> intBuffer;
}
```

## Specialization ##
If a specific type needs a different implementation, this can be defined with
specialization.  Code is in addition to the template:
```
template <>
void ExampleClass<double>::function(<double> t)
{
    // ...
};
```

Class methods and entire classes can also be specialized with similar syntax.
There is no rule that a specialization of a class needs to implement the same
functionality, but generally it's a good idea.  (Otherwise, why are you using
templates anyway?)

### Partial Specialization ###
In some cases, it is necessary to have specialization where some of the template
parameters are still unspecified.  In this case, mix the template types and/or
parameters with specified instances:
```
template <int nSize>
void printBuffer(Buffer<char, nSize> &buffer)
{
    // ...
}
```

### Partial Specialization with Pointers ###
Because copying pointers is different from copying basic values, it's useful to
have a partially specialized template for pointer values.  This is achieved very
simply:
```
template <typename T>
class Storage<T*>
{
private:
    T* m_tValue;
public:
    Storage(T* tValue)
    {
        m_tValue = new T(*tValue);
    }
};
```
