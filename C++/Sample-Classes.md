# Sample Classes #
sample1.cpp
```cpp
class Example1
{
// member vars are private by default, but this is an example
private:
    int x;
    int y;
    int z;
    static int static_val;              // MUST initialize outside of class
    static const int days_in_week = 7;  // MUST initialize here
public:
    int q;
    // C++11:  default value; overridden by any relevant constructors
    double cpp11only {1.0};

public:
    // constructor; note lack of return, default function values
    Example1(int x=1, int y=1, int z=1)
    {
        this->x = x;
        this->y = y;
        this->z = z;
    }

    // constructor; direct initialization of member values
    Example1(int a, int b, int c) : x(a), y(b), z(c)
    {
        // deliberately empty
    }

    // destructor; note lack of parameters and return
    ~Example1()
    {
        // must return dynamically allocated memory here
        // release other resources (e.g. database locks)
        // exit() will bypass this when terminating a process
    }
};
// class definition MUST end with semicolon

// Static variable initialized outside the class
Example1::static_val = 5;
```

sample2.h
```cpp
#IFNDEF SAMPLE2_H
#DEFINE SAMPLE2_H
class Example2
{
private:
    int x;
    int y;
    int z;
public:
    int q;

public:
    Example2(int x, int y, int z);
private:
    void comp_q();
};
#ENDIF
```

sample2.cpp
```cpp
#include "sample2.h"

Example2::Example2(int x, int y, int z)
{
    this->x = x;
    this->y = y;
    this->z = z;
}

void Example2::comp_q()
{
    q = x + y + z;
}
```
