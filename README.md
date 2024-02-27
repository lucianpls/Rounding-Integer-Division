# Rounding-Integer-Division
Integer division in C and C++ truncate towards zero. Sometimes rounding is more appropriate, but it's not trivial. The common solution is to convert to double, add 0.5 then perform floating point division and convert the result back to integer. It works, but it's inefficient and will produce errors when the integers have higher values than double precision floating points can accurately accomodate.


This solution works entirely in the integer space, no overflow possible. The only limitation is that d has to be positive.  The difference between the two functions is the direction of the rounding, which can be towards or away from zero.

```
template<typename T>
T math_rounddiv_from_0(T x, T y) {
    return x / y + (x % y) / (y / 2 + y % 2);
}

template<typename T>
T math_rounddiv_to_0(T x, T y) {
    return x / y + (x % y) / (y / 2 + 1);
}
```

The template functions above work well, but they are relatively slow since they require at least two chained integer divisions.
The following C++ function templates are optimized versions that only use one division, operating at roughly the same speed as the normal (truncating) integer division on a modern CPU.
For performance resons it is helpful to declare the type of y as unsigned.

```
// Round from Zero Division, no overflow
template<typename T>
static T rounding_from_0_div(T x, T y) {
    T r = x / y, m = x % y;
    y = (y >> 1) + (y & 1);
    return r + (~(x < 0) & (m >= y)) - ((x < 0) & (m <= -y));
}

// Round to Zero Division, no overflow
template<typename T>
static T rounding_to_0_div(T x, T y) {
    T r = x / y, m = x % y;
    y >>= 1;
    return r + (~(x < 0) & (m > y)) - ((x < 0) & (m < -y));
}
```

If y can be negative, wrap the chosen function in a macro or template, using `((y < 0)?-f(x,-y):f(x,y))`. The only problem left is when d == INT_MIN for signed types and of course d == 0. It will be 
slower due to the explicit branch or conditional assignment, especially when the sign of y is unpredictable.

