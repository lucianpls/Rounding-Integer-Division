# Rounding-Integer-Division
Integer division in C truncates towards zero. Sometimes rounding is more appropriate, but it's not trivial.  

These portable C++ function templates implement rounding integer division for any integral types, signed or unsigned, without danger of overflow and without conversion to floating point types. The divisor `y` has to be positive and non-zero.


```
// Round from Zero Division, no overflow
template<typename T>
static T rfr0div(T x, T y) {
    T r = x / y, m = x % y;
    y = (y >> 1) + (y & 1);
    return r + (~(x < 0) & (m >= y)) - ((x < 0) & (m <= -y));
}

// Round to Zero Division, no overflow
template<typename T>
static T rto0div(T x, T y) {
    T r = x / y, m = x % y;
    y >>= 1;
    return r + (~(x < 0) & (m > y)) - ((x < 0) & (m < -y));
}
```
