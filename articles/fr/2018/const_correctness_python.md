## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Const-correctness en Python ?

---

### Sommaire


---

### Introduction
```ml
let coords = (10, 12);; (* 'coords' est immuable *)
let x, y = coords;;
```


---

### Exemple d'implémentation

Version C++ :
```cpp
#include <type_traits> // std::is_integral

template<class T>
struct Point final {
  static_assert(std::is_integral<T>::value, "std::is_integral<T>");
public:
  Vector2(T _x, T _y) : x{_x}, y{_y} {}
  const T x;
  const T y;
};
```

Version Python :
```py
class Point(object):
    def __init__(self, x, y):
        is_number = lambda n: isinstance(n, int) or isinstance(n, float)
        assert is_number(x), "assertion 'is_number(x)' failed"
        assert is_number(y), "assertion 'is_number(y)' failed"
        # Optionnel :
        #assert type(x) == type(y), "assertion 'type(x) == type(y)' failed"
        self._x = x
        self._y = y

    @property
    def x(self):
        return self._x

    @property
    def y(self):
        return self._y
```
