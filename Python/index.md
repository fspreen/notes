# Python Topics

* [Virtual Environments](Virtual-Environments.md)

## Snippets

### Properties
Most objects automatically have a `__dict__` property which contains all the
properties of the object as a dictionary.  This is useful for introspection and
debugging, but not all objects have `__dict__`.  Notably, classes that make use
of `__slots__` do not have a `__dict__`.  The following snippet makes use of the
built-ins `dir()` and `getattr()` to produce a `__dict__` like structure:

```python
props=lambda x: {k:getattr(x,k,None) for k in dir(x) if not callable(getattr(x,k,lambda z:z)) and k!='__slots__' and k!='__dict__'}
```

## 2 and 3 Compatibility

### Input Functions
Python 3 makes changes to the `input()` and `raw_input()` functions that do not
fit with Python 2.  In Python 2, `raw_input()` reads a string from standard
input while `input()` reads and _evaluates_ that string.

Python 3 does not have any function named `raw_input()` but changes the
`input()` function to only read a string (i.e. the `input()` of Python 3 is
equivalent to the `raw_input()` of Python 2).

Naively using `input()` or `raw_input()` in Python code will limit that code to
either Python 2 or Python 3, since the behaviors are different.  Unfortunately
there is no `__future__` import that will cover this case.

The following two lines replace `input()` with `raw_input()` in Python 2 code.
This allows the function `input()` to be used in an identical fashion in both
Python 2 and 3.
```python
try: input = raw_input
except NameError: pass

# ...

s = input("> ")
```
