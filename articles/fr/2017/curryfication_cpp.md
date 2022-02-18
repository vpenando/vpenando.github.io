#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Réflexion - Application partielle de fonction en C++

###### Note importante
> Cet article est une réflexion personnelle. Il ne s'agit pas d'un article structuré visant à apprendre ou détailler des points du langage. Par ailleurs, je compile en C++14, aussi je vous recommande d'utiliser un compilateur supportant cette norme si vous souhaitez reproduire ces exemples (VS2015/2017 ou un GCC/Clang récent).

---

Dans certains langages, et notamment en programmation fonctionnelle, il est possible de profiter d'un mécanisme appelé *application partielle*. Cela consiste à appeler une fonction avec seulement une partie de ses arguments afin de générer une nouvelle fonction.

Et, parce qu'un exemple est toujours plus parlant (ici en Caml) :

```ocaml
(* Fonction addition basique *)
let add = fun x y -> x + y;; (* int -> int -> int *)

(* Application partielle de add *)
let add2 = add 2;; (* int -> int *)
```
Dans ce code, nous définissons deux fonctions : `add`, qui renvoie la somme de deux nombres `x` et `y`, et `add2`, application partielle de `add`, qui ajoute 2 à un nombre `x`. Essayons d'appliquer ce mécanisme au C++.

```cpp
// Fonction addition basique
const auto add = [](int x, int y) -> int { return x + y; };

// Essayons d'en faire une application partielle :
const auto add2 = add(2);
```
Si nous essayons de compiler un code similaire, que se passe-t-il ? 
```
note: candidate function not viable: requires 2 arguments, but 1 was provided
   const auto add = [](int x, int y) -> int { return x + y; };
                    ^
```
En clair, le compilateur nous informe que nous essayons d'appeler une fonction attendant deux arguments en n'en fournissant qu'un seul. Ne comprend-il pas que nous essayons de créer une nouvelle fonction ? Alors bien sûr, il y a une solution viable, bien que ce ne soit pas ce que nous essayons de faire :

```cpp
const auto add2 = [](int x) -> int { return add(2, x); };
```
Nous définissons explicitement une lambda `add2` qui appelle `add` avec 2 et `x`.

A présent, essayons de créer notre mécanisme d'application partielle. Nous allons définir une fonction similaire à `std::bind`. Pour ceux qui ne connaissent pas, `std::bind` permet de faire ce genre de chose :

```cpp
// Reprenons notre fonction add
const auto add = [](int x, int y) -> int { return x + y; };

namespace ph = std::placeholders;
const auto add2 = std::bind(add, 2, ph::_1); // add2 est bien une fonction !
// Cela équivaut à la version de add2 présentée plus haut.
```
Mais alors, `std::bind` est la solution à notre problème ! On peut appliquer partiellement une fonction grâce à lui ! C'est très bien, mais `std::bind` a un défaut, à savoir qu'il nous faut spécifier les placeholders. Cela peut être intéressant dans certains cas (par exemple, application partielle d'une fonction en mélangeant les arguments), mais c'est aussi handicapant.

Quid donc de définir une fonction similaire sans cette contrainte ? Pour cela, il est nécessaire de connaître des principes tels que :
* Les templates
* Les variadic templates
* Les lambdas

```cpp
namespace fun { // "fun" pour "functional"

    // Application partielle en ne spécifiant qu'un argument
    template<class Fn, class T>
    auto apply(Fn const& fn, T const& val) {
        // On renvoie simplement une lambda prenant des arguments variadiques
        return [=](auto&& ...a) {
            return fn(val, a...); // Appel de fn avec tous ses arguments
        };
    }

    // Application partielle en spécifiant plusieurs arguments
    template<class Fn, class ...Args>
    auto apply(Fn const& fn, Args&& ...args) {
        return [=](auto&& ...a) {
            return fn(args..., a...);
        };
    }
  
} // namespace fun
```
A l'usage, à quoi cela ressemble-t-il ?

```cpp
const auto add  = [](int x, int y) -> int { return x + y; };
const auto add2 = fun::apply(add, 2); // Pas besoin de placeholder !
const auto five = add2(3);
```

A titre d'exemple, voici un programme complet ainsi que sa sortie, disponible sur [coliru](http://coliru.stacked-crooked.com/a/52bf27fed21e1cda) :

```cpp
#include <iostream> // std::cout, std::endl

namespace fun {

    template<class Fn, class T>
    auto apply(Fn const& fn, T const& val) {
        return [=](auto&& ...a) {
            return fn(val, a...);
        };
    }

    template<class Fn, class ...Args>
    auto apply(Fn const& fn, Args&& ...args) {
        return [=](auto&& ...a) {
            return fn(args..., a...);
        };
    }
  
} // namespace fun

void print_args(int a, int b, int c, int d) {
    std::cout << a << std::endl;
    std::cout << b << std::endl;
    std::cout << c << std::endl;
    std::cout << d << std::endl;
}

int main() {
    const auto test = fun::apply(print_args, 1, 2);
    test(3, 4);
}
```
Sortie :
```cpp
1
2
3
4
```

---
#### Conclusion
Suite à un post récent sur un forum, je m'étais demandé s'il était possible de coder une fonction d'application partielle en C++14. Il s'est avéré que non seulement c'était possible, mais qu'il était surtout possible de faire ça *simplement*. En effet, deux fonctions suffisent pour cela !
