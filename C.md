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

## Dynamic Memory ##
Memory can be dynamically allocated by use of the `malloc()` function, and
released from allocation by use of the `free()` function.  Since `malloc()`
return a pointer of type `void *` it is best to cast the pointer to a specific
type before using it.

Many different bugs are possible in C relating to dynamic memory
(mis)management.  For this reason, more modern languages allow less freedom
regarding dynamic memory resources

Common issues include:  allocation failure, memory leak (not freeing memory,
causing increased memory usage), using a pointer after freeing the memory, using
a pointer before allocating the memory, freeing a pointer twice, and so on.
These problems are often difficult to debug due to the fact that it involves
uninitialized memory values, which may be anything and are not under control or
review by the code.

The `malloc()` and `free()` functions are part of the `stdlib.h` standard
header.
