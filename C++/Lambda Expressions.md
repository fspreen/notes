# Lambda Expressions #
Added in C++11

## Basic Syntax ##
This is the basic syntax of a lambda expression, although some parts are
optional:
```
[captures] (parameters) -> returnType { statements; }
```

The brackets indicate which outside variables should be "captured" by the lambda
function, if any.  The brackets are _not_ optional and can always be used to
identify a lambda expression.

The parameters act similar to function arguments.  In the case of zero
parameters, they can be replaced with the keyword `void` or an empty set of
parenthesis, or the parenthesis can be omitted entirely.

The return type can be specified here similar to the special syntax for function
declarations.  It may also be omitted, in which case the compiler attempts to
deduce it from the body of the expression.

## Capture Lists ##
Captured variables (if any) are listed in the square brackets and separated by
commas.  A captured variable must not be listed more than once.  Variables can
be captured by value or by reference.

If the capture list consists of `[&]`, any variables used in the body of the
lambda are captured by reference.  If the capture list consists of `[=]`, they
are captured by value.  Alternatively, variables can be listed after the `&` or
`=` special tokens, but they must not be duplicated as per above.  (For example,
the capture list `[&, &i]` is wrong because `&i` is effectively listed twice.

Because non-static class methods have the implicit `this` pointer, capturing
`this` is always by reference.  (C++17 is slated to allow `*this` for capturing
the current object by value.)
