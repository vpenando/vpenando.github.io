## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) `const`, `constexpr`, `#define` ?

---

### Sommaire
* [Introduction](#introduction)

---

### Introduction
En C++, on parle souvent de *const-correctness*. Il s'agit d'une bonne pratique visant à promouvoir l'usage de constantes autant que possible.
Certains langages, notamment en programmation fonctionnelle, rendent par défaut toutes les variables constantes :
```fs
let foo = 42;;  (* 'foo' n'est pas modifiable *)
```
En C++, par défaut, toute variable est modifiable :
```cpp
int foo = 42;
foo = 12;
```


---

### `constexpr` vs `const`



---

### `constexpr` vs `#define`

* scope global
* redéfinitions ? (ex : dans un namespace)
* typage
* template

```cpp
#define PI 3.14159265359

template<class T>
constexpr static T PI = static_cast<T>(3.14159265359);
```
