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

En ce qui me concerne, je suis un peut touche à tout ; je fais du C++, du C#, du F#/OCaml... mais aussi du scripting, et ce particulièrement en Python. C'est un langage qui est élégant et avec lequel on peut prototyper rapidement. Cependant, ce langage *-comme la plupart des langages de script-* me pose un petit problème : en Python, les variables, en plus d'être dynamiquement typées, ne peuvent pas être déclarées comme constantes. 

---

### Exemple

D'une manière générale, je tends à déclarer presque *toutes* mes variables comme constantes (tant que cela a un sens et que c'est possible). En effet, dès lors qu'une entité a sémantique de valeur (par opposition à celles à sémantique d'entité - [Source](https://blog.emmanueldeloget.com/index.php?post/2011/11/18/Standard-C11-%3A-la-s%C3%A9mantique-de-d%C3%A9placement)), il est assez fréquent de pouvoir aisément la déclarer comme constante.

Reprenons l'exemple des coordonnées X et Y :
```cpp
#include <type_traits> // std::is_integral

template<class T>
struct Point final {
  static_assert(std::is_integral<T>::value, "Expected integral type");
public:
  Point(T _x, T _y) : x{_x}, y{_y} {}
  const T x;
  const T y;
};

// Et à l'usage :
Point<int> point{10, 12};
// Provoquera une erreur :
point.x = 42;
```
([Exemple de résultat avec un tel code](http://coliru.stacked-crooked.com/a/e8dde388c7cb68d8))
Ainsi, je tends à limiter les effets de bord et à forcer les utilisateurs de mon code de respecter cette philosophie. Ce n'est ni plus ni moins ce que font beaucoup de langages fonctionnels (OCaml, F#, voire Elm pour des applications Web).

Question : peut-on aboutir à un tel résultat en Python ?

---

### Implémentation en Python

Contre toute attente, la réponse est... non. On peut néamoins s'approcher de ce comportement grâce à l'utilisation des propriétés, pour rendre `x` et `y` constants.

Parce que du code vaut mieux qu'un long discours :
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
