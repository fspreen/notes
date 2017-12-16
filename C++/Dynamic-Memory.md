# Dynamic Memory #

Memory in C++ _can_ be allocated identically to C, but it is highly discouraged
to do so.  Instead, C++ offers the built-in `new` and `delete` keywords.

## Allocation ##
The `new` operator allocates memory and returns a specific type of pointer:
```cpp
int *ptr = new int;
```

Arrays are allocated similarly, but size is included:
```cpp
int *array = new int[5];
```

## Deallocation ##
The `delete` operator frees memory for later use.  It is a good idea to set the
pointer to `nullptr` after deleting, just to be safe.  (Prior to C++11, there
was no null type, so 0 was used.)
```cpp
int *ptr = new int;
// ...
delete ptr;
ptr = nullptr;
```

For dynamically allocated arrays, the keyword `delete[]` should be used instead:
```cpp
int *array = new int[5];
// ...
delete[] array;
```

Failure to use `delete[]` instead of `delete` can lead to data corruption,
memory leaks, or other issues.
