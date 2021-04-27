## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) À la découverte du langage Elm !

---

### Introduction


- Functions are pure (i.e. there are no side-effects).
- The language is strongly and statically typed, so you have to make sure that all pieces neatly fit together before you can run your code.
- All data is immutable.


---

### Pourquoi choisir Elm ?

- Pas de valeur nulle/undefined (`Maybe` à la place, comme en Haskell, équivalent à `Option` en OCaml, Rust, etc)
- Pas d'erreur au runtime
- Interopérabilité avec JS
- Atouts de la programmation fonctionnelle (application partielle, pattern matching, types sommes, ...)

---

### Présentation

#### Fonctions et variables
Déclarer une variable en Elm est on ne peut plus simple :
```elm
hello = "Hello, world!"
```
Et c'est tout !
Cette variable est fortement typée et surtout, elle est immutable !
Une fois définie, il est **impossible de la modifier**. Cela peut sembler pénalisant au premier abord, mais c'est en réalité un atout majeur.
En effet, cela induit que :
* Aucun effet de bord ne peut modifier une variable ;
* Toute variable est thread-safe ;
* Facile à déboguer : contrairement à d'autres langages, nul besoin de chercher où elle est modifiée !

En Elm, toute fonction est également une variable. Ainsi, déclarer une fonction se fait ainsi :
```elm
add x y = x + y
```
Il est également possible *-et recommandé-* d'annoter une fonction de sa signature, comme suit :
```elm
add : Int -> Int -> Int
add x y = x + y
```
L'annotation de `add` peut à première vue sembler confuse. En effet, cette fonction attend deux variables de types `Int` et renvoie un résultat, lui-même de type `Int`.
Ainsi, on pourrait de prime abord s'attendre à une signature proche de `(Int, Int) -> Int`. Néanmoins, il faut plutôt voir `add` comme une fonction attendant un argument de type `Int` et renvoyant une autre fonction. Exemple : `add2 = add 2`, dont la signature est `Int -> Int`.
Vous l'aurez donc compris, on peut profiter de l'*application partielle* en Elm !

