# Rounding-Integer-Division
Integer division in C and C++ truncate towards zero. Sometimes rounding is more appropriate, but it's not trivial. The common solution is to convert to double, add 0.5 then perform floating point division and convert the result back to integer. It works, but it's inefficient and will produce errors when the integers have higher values than double precision floating points can accurately accomodate.

These portable C++ function templates implement rounding integer division for any integral types, signed or unsigned, without danger of overflow and without conversion to floating point types. The divisor `y` has to be positive and non-zero. If y is negative, use -f(x,-y).


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

Alternate solution that uses two divisions, so it's slower. Works for signed x, as long as y is positive.
```
template<typename T>
T math_rounddiv(T x, T y) {
    return x / y + (x % y) / (y / 2 + 1);
}

template<typename T>
T math_rounddiv_away(T n, T d) {
    return x / y + (x % y) / (y / 2 + y % 2);
}
```
