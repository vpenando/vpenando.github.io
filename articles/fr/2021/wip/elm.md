## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) À la découverte du langage Elm !

---

### Introduction

- Langage orienté front
- Compilé / transpilé


---

### Pourquoi choisir Elm ?

- Pas de valeur nulle/undefined (`Maybe` à la place, comme en Haskell, équivalent à `Option` en OCaml, Rust, etc)
- Pas d'erreur au runtime
- Interopérabilité avec JS
- Performances

---

### Présentation

#### Variables
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
* Facile à déboguer : contrairement à d'autres langages, nul besoin de chercher où une variable est modifiée !

Elm dispose non seulement des types primitifs "classiques" `Int`, `Float`, `Bool`, `Char` et `String`, mais également des types tels que `Maybe a`, `List a`, où `a` désigne un type quelconque. Le type `Maybe`désigne une valeur optionnelle et est l'équivalent de `Option` en OCaml ou Rust (entre autres), et est similaire au type éponyme existant en Haskell. Il s'agit de la meilleure réponse possible aux problèmes posés par des valeurs telles que `null` ou `undefined`.
```elm
someInt = 42
someFloat = 3.14
someBool = True
someChar = 'A'
someString = "Hello, world!"
someList = [1, 2, 3, 4, 5]
```
`Maybe a` propose les variants `Just a` et `Nothing`, correspondant respectivement à la présence et l'absence de valeur :
```elm
a = Just 42  -- on a une valeur, ici 42
b = Nothing  -- pas de valeur !
```
***Note -** Si vous êtes étranger à la programmation fonctionnelle, considérez simplement le type `Maybe` comme une forme d'`enum` améliorée.*

#### Fonctions
En Elm, toute fonction est également une variable. Ainsi, déclarer une fonction se fait ainsi :
```elm
add x y = x + y
```
Il est également possible *-et recommandé-* d'annoter une fonction de sa signature, comme suit :
```elm
add : Int -> Int -> Int
add x y = x + y

-- et à l'usage :
five = add 2 3 -- pas de parenthèses nécessaires !
```
L'annotation de `add` peut à première vue sembler confuse. En effet, cette fonction attend deux variables de types `Int` et renvoie un résultat, lui-même de type `Int`.
Ainsi, on pourrait de prime abord s'attendre à une signature proche de `(Int, Int) -> Int`. Néanmoins, `add` est une fonction attendant un argument et renvoyant une autre fonction attendant un argument. Plus d'informations [ici](https://guide.elm-lang.org/appendix/function_types.html).
Vous l'aurez donc probablement compris, on peut profiter de l'*application partielle de fonction* en Elm !

#### Application partielle de fonction
Conceptuellement, l'application partielle de fonction consiste à appeler une fonction avec seulement une partie de ses arguments.
En résulte une nouvelle fonction, qui attend le reste des arguments.
```elm
add : Int -> Int -> Int
add x y = x + y

add2 : Int -> Int
add2 x = add 2
```
En combinaison avec des fonctions telles que [`List.map`](https://package.elm-lang.org/packages/elm/core/latest/List#map) ou [`List.filter`](https://package.elm-lang.org/packages/elm/core/latest/List#filter), il devient alors aisé de créer des fonctions spécialiées et réutilisables !

#### 
