## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) C++20 et concepts - La fin des templates ?

---

### Sommaire


---

### Introduction

```cpp
template<typename T>
concept bool Integral() {
    return std::is_integral<T>::value;
}

void foo(Integral i) {
    std::cout << i;
}
```

* Invariants vérifiés par le type lui-même
