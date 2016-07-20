# C #

## Const and Pointers ##
```
const int * num;
int const * num;
```
These are pointers to constant data.  In this case, `num` is a pointer to a
constant integer.  The pointer is changeable, but the integer value is not.
```
int * const num;
```
This is a constant pointer to data.  In this case, `num` is a constant pointer
to an integer.  The pointer is not changeable, but the integer value is.  These
pointers should be assigned an immediate value, since they cannot be changed.
(Example is a function taking a pointer to a variable on the stack.)
```
const int * const num;
int const * const num;
```
These are constant pointers to constant data.  Neither the pointer value nor the
integer value can be changed.
