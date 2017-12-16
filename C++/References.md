# C++ References #
A reference is a type of variable standing between a direct value and a pointer.
Effectively, a reference acts like a const pointer that is implicitly
dereferenced when accessed.  (Note the difference between _const pointer_ and
_pointer to const_, although references can also refer to const values.)
```cpp
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
