## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) `const`, `constexpr`, `#define` ?

---

### Sommaire
* [Introduction](#introduction)

---

### Introduction
En C++, on parle souvent de *const-correctness*. Il s'agit d'une bonne pratique visant à promouvoir l'usage de constantes autant que possible. Cela nous permet d'éviter de modifier des variables qui ne devraient pas l'être.

Prenons l'exemple suivant :
```cpp
int SDL_BlitSurface(SDL_Surface*    src,
                    const SDL_Rect* srcrect,
                    SDL_Surface*    dst,
                    SDL_Rect*       dstrect);

int result = SDL_BlitSurface(src, srcrect, dst, dstrect);
if (result = 0) {  // /!\ Erreur /!\
    //     ^  ici
}
```

Certains langages, notamment en programmation fonctionnelle, rendent par défaut toutes les variables constantes :
```fs
let foo = 42;;  (* 'foo' n'est pas modifiable *)
```
En C++, comme dans beaucoup de langages, toute variable est par défaut modifiable :
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
