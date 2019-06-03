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
En C++, nous avons de nombreux moyens de déclarer des constantes. C++11 a par ailleurs ajouté le mot-clé `constexpr`, que je vais comparer aux moyens connus.

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
