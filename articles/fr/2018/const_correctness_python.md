## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Const-correctness en Python ?

---

### Sommaire


---

### Introduction

En programmation, il est assez courant de vouloir déclarer certaines variables comme immuables. Cela évite certains effets de bords, et la phase de debug s'en retrouve en conséquence allégée.

Prenons l'exemple d'une fonction quelconque :
```cpp
void foo(int x, int y) {
  Point point{x, y}; // Admettons qu'on veuille jouer avec des coordonnées
  // Du code
  // ...
  // Encore du code
  // ...
  // Toujours plus de code
  // ...
}
```
A la simple lecture du début de cette fonction, on ne sait pas encore si `point` sera voué à être modifié. A cet effet, on aura instinctivement envie de chercher où cette variable est modifiée. En effet, il se peut que les coordonnées soient corrigées si `x` ou `y` ne correspond pas à une condition donnée. Mais il se peut également (dans la plupart des cas à vrai dire) que `point` ne soit jamais modifié.

Il y a des langages où la question ne se pose même pas :
```ml
let foo x y =
  let point = (x, y) in
  (* suite de la fonction... *)
  ;;
```
Dans ce cas précis, et compte tenu du langage utilisé (OCaml), on sait d'avance que `point` est immuable. A cet effet, nul besoin de se demander si et où cette variable sera modifiée.

En ce qui me concerne, je suis un peut touche à tout ; je fais du C++, du C#, du F#, mais aussi du scripting, et ce particulièrement en Python. C'est un langage qui est élégant et avec lequel on peut prototyper rapidement. Cependant, ce langage *-comme la plupart des langages de script-* me pose un petit problème : en plus d'être dynamiquement typées, les variables ne peuvent pas être déclarées comme constantes. 

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
