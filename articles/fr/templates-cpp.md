

Avant :
```cpp
template<unsigned N>
struct Factorial {
  static_assert(N <= 10, "Invalid value for N (max value: 10)");
  constexpr static unsigned value = N * Factorial<N - 1u>::value;
};

struct default_fac {
  constexpr static unsigned value = 1u;
};

template<> struct Factorial<1u> : default_fac {};
template<> struct Factorial<0u> : default_fac {};

template<int N, unsigned P>
struct Pow {
  constexpr static int value = N * Pow<N, P - 1u>::value;
};

template<int N>
struct Pow<N, 1u> {
  constexpr static int value = N;
};
```

Après :
```cpp
template<unsigned N>
struct Factorial : std::integral_constant<decltype(N), N * Factorial<N - 1u>::value> {
  static_assert(N <= 10, "Invalid value for N (max value: 10)");
};

using default_fac = std::integral_constant<unsigned, 1u>;

template<> struct Factorial<1u> : default_fac {};
template<> struct Factorial<0u> : default_fac {};

template<int N, unsigned P>
struct Pow : std::integral_constant<decltype(N), N * Pow<N, P - 1u>::value> {};

template<int N>
struct Pow<N, 1u> : std::integral_constant<decltype(N), N> {};

template<int N>
struct Pow<N, 0u> : std::integral_constant<decltype(N), 1u> {};
```

`std::integral_constant`:
```cpp
template<class T, T v>
struct integral_constant {
    static constexpr T value = v;
    typedef T value_type;
    typedef integral_constant type; // using injected-class-name
    constexpr operator value_type() const noexcept { return value; }
    constexpr value_type operator()() const noexcept { return value; } //since c++14
};
```
