## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) C++20 et concepts - La fin des templates ?

---

### Sommaire


---

### Introduction

```cpp
template<class T>
concept Integral = std::is_integral_v<T>;

void foo(Integral i) {
    std::cout << i;
}
```

* Invariants vérifiés par le type lui-même
* GCC : `-std=c++2a -fconcepts`

---

### Concepts vs std::enable_if
```cpp
// plutôt que :
template<class T>
using Integral = std::enable_if_t<std::is_integral_v<T>, T>;

template<class T>
void foo(Integral<T> i) {
    std::cout << i;
}
```
