## Templates, récusivité et `std::integral_constant`
Calculs par récursivité

## Avant-propos
Nous allons utiliser la classe `std::integral_constant` pour la suite. Commençons donc par la présenter.
Qu'est-ce que `std::integral_constant` ?
```cpp
namespace std {

  template<class T, T v>
  struct integral_constant {
    static constexpr T value = v;
    typedef T value_type;
    typedef integral_constant type; // using injected-class-name
    constexpr operator value_type() const noexcept { return value; }
    constexpr value_type operator()() const noexcept { return value; } //since c++14
  };
  
} // namespace std
```
Très simplement : une classe qui prend en paramètre template un type `T` et une instance de ce type `v`, accessible par sa variable membre statique `value`.
Exemple : `std::integral_constant<int, 12>::value` renverra 12. Cela n'a l'air de rien, mais c'est vraiment puissant. Vous ne me croyez pas ? La suite vous montrera pourquoi.

## Le cas simple : le calcul récursif
### I - La factorielle
**1.1 - La factorielle**, sans `std::integral_constant`
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
```

**1.2 - La factorielle** - Version 2, avec `std::integral_constant`
```cpp
template<unsigned N>
struct Factorial : std::integral_constant<decltype(N), N * Factorial<N - 1u>::value> {
  static_assert(N <= 10, "Invalid value for N (max value: 10)");
};

using default_fac = std::integral_constant<unsigned, 1u>;

template<> struct Factorial<1u> : default_fac {};
template<> struct Factorial<0u> : default_fac {};
```
**Note -** `decltype(N)` renvoie le type de `N`, ici `unsigned`. C'est utile de l'utiliser pour minimiser le refactoring en cas de changement de type.

**1.3 - Utilisation**
```cpp
int main() {
  constexpr auto fac10 = Factorial<10>::value; // Ou Factorial<10>()
  std::cout << fac10; // 3628800
}
```

### II - La puissance
**2.1 - La puissance**, sans `std::integral_constant`
```cpp
template<int N, unsigned P>
struct Pow {
  constexpr static int value = N * Pow<N, P - 1u>::value;
};

template<int N>
struct Pow<N, 1u> {
  constexpr static int value = N;
};

template<int N>
struct Pow<N, 0u> {
  constexpr static int value = 1;
};
```

**2.2 - La puissance**, avec `std::integral_constant`
```cpp
template<int N, unsigned P>
struct Pow : std::integral_constant<decltype(N), N * Pow<N, P - 1u>::value> {};

template<int N>
struct Pow<N, 1u> : std::integral_constant<decltype(N), N> {};

template<int N>
struct Pow<N, 0u> : std::integral_constant<decltype(N), 1u> {};
```

**2.3 - Utilisation**
```cpp
int main() {
  constexpr auto pow2_3 = Pow<2, 3>::value; // Ou Pow<2, 3>()
  std::cout << pow2_3; // 8
}
```

## Usages "avancés"
Contrainte sur des types à la compilation :
```cpp
template<unsigned N, template<class> class Fn, class ...Args>
struct IndividualCheck : std::conditional<N == 0, std::true_type, IndividualCheck<N-1, Fn, Args...>>::type {
private:
  using Base = typename std::conditional<N == 0, std::true_type, IndividualCheck<N-1, Fn, Args...>>::type;
  using Type = typename std::tuple_element<N, std::tuple<Args...>>::type;
  constexpr static bool condition = Fn<Type>::value;
public:
  constexpr static bool value = condition && Base::value;
  constexpr bool operator()() noexcept { return value; }
};

template<template<class> class Fn, class ...Args>
struct BaseCheck : IndividualCheck<sizeof...(Args) - 1, Fn, Args...> {};

template<template<class> class Fn, class ...Args>
struct Check : BaseCheck<Fn, Args...> {};
```

Exemples :
```cpp
template<class T>
struct is_number : std::is_arithmetic<T> {};

int main(int argc, char **argv) {
  std::cout << std::boolalpha;
  std::cout << "Valid check? " << Check<is_number, int, double, float>::value << std::endl; // true
  std::cout << "Valid check? " << Check<is_number, int, double, char*>::value << std::endl; // false
  // Versions équivalentes :
  std::cout << "Valid check? " << Check<is_number, int, double, float>() << std::endl; // true
  std::cout << "Valid check? " << Check<is_number, int, double, char*>() << std::endl; // false
}
```

```cpp
template<class T>
struct is_number : std::is_arithmetic<T> {};

template<class ...Args>
void foo(std::tuple<Args...> const& tuple) {
  constexpr auto contains_only_nums = Check<is_number, Args...>();
  static_assert(contains_only_nums, "Invalid tuple");
}

int main() {
  foo(std::tuple<int, int, char*>{});
}
```
