## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) `const`, `constexpr`, `#define` ?

---

### Sommaire
* [Introduction](#introduction)

---

Introduction
---
En C++, on parle souvent de *const-correctness*. Il s'agit d'une bonne pratique visant à promouvoir l'usage de constantes autant que possible. Cela nous permet d'éviter de modifier des variables qui ne devraient pas l'être.

Prenons l'exemple suivant :
```cpp
// cela marche avec toute fonction renvoyant un code d'erreur,
// mais SDL_BlitSurface a l'avantage d'être bien connue.
int SDL_BlitSurface(SDL_Surface*    src,
                    const SDL_Rect* srcrect,
                    SDL_Surface*    dst,
                    SDL_Rect*       dstrect);

int result = SDL_BlitSurface(src, srcrect, dst, dstrect);
if (result = 0) {  // /!\ Erreur : un '=' ou un '!' a été oublié  /!\
    //     ^  ici
}
```
Que se passe-t-il ici ? La variable `result`, quelle que soit sa valeur, est réassignée dans le `if` de manière à valoir 0. Ce faisant :
* La condition est **toujours** évaluée à `false` ;
* La précédente valeur de `result` est écrasée.

Ainsi, nous ne savons pas si l'appel à `SDL_BlitSurface` a réussi ou échoué. Une solution possible serait de passer la variable `result` constante de manière à avoir une erreur de compilation :
```
error: cannot assign to variable 'result' with const-qualified type 'const int'
```

Certains langages, notamment en programmation fonctionnelle, rendent par défaut toutes les variables constantes :
```fs
let foo = 42;;  (* 'foo' n'est pas modifiable *)
```
En C++, nous avons de nombreux moyens de déclarer des constantes. C++11 a par ailleurs ajouté le mot-clé `constexpr`, que je vais comparer aux habituels `const` et `#define`.

---

`constexpr` vs `const`
---
Comme vu dans la partie précédente, `const` permet de déclarer une variable constante :
```cpp
const int i = 42;  // i n'est pas modifiable
```
Le mot-clé `const` peut être placé avant ou après le type, sans réelle incidence :
```cpp
const int i = 42;
// équivaut à :
int const i = 42;
```
Une petite subtilité existe cependant, dans le cas des pointeurs :
```cpp
const int *ptr = 42;
// n'est pas la même chose que :
int* const ptr = 42;
```
Dans le premier cas, la valeur pointée par `ptr` n'est pas modifiable. Il n'est donc pas possible d'assigner une valeur à `*ptr`.

Dans le second cas, le pointeur n'est pas modifiable, mais on peut assigner une valeur à `*ptr`.

***Note -** Pour déclarer un pointeur constant vers une valeur constante, on utilise `const T* const`.*

Le mot-clé `constexpr` permet quant à lui de déclarer des constantes évaluées à la compilation. Cependant, ces dernières doivent pouvoir être évaluées à la compilation. Un tel code n'est pas valide :
```cpp
int value() {
    int i = 0;
    std::cin >> i;
    return i;
}

const auto i1 = value();      // ok
constexpr auto i2 = value();  // pas ok
```
Mais du coup, si `constexpr` est plus contraignant que `const`, quel est son intérêt ?

Outre le fait qu'une variable `constexpr` est forcément une constante de compilation, on peut également y appliquer un template :
```cpp
template<int I>
constexpr auto square = I * I;
```
C'est par ailleurs ainsi que sont définies des variables telles que [`std::is_same_v`](https://en.cppreference.com/w/cpp/types/is_same) (qui n'est en définitive qu'un alias pour `std::is_same<T, U>::value`).

Enfin, une fonction peut également être `constexpr` !
```cpp
constexpr size_t fac(size_t value) noexcept {
  return (value == 0) ? 1 : value * fac(value - 1);
}

// et à l'usage :
constexpr auto fac5 = fac(5);
```
Et, mieux encore une fonction avec un template :
```cpp
template<class T>
constexpr T fac(T value) noexcept {
  return (value == 0) ? 1 : value * fac(value - 1);
}

// et à l'usage :
constexpr auto fac5 = fac<size_t>(5);
```

---

`constexpr` vs `#define`
---

* scope global
* redéfinitions ? (ex : dans un namespace)
* typage
* template

```cpp
#define PI 3.14159265359

template<class T>
constexpr static T PI = static_cast<T>(3.14159265359);
```