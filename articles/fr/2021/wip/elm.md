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

#### Pattern matching

**Todo**

`switch`/`case` amélioré

#### Définir ses propres types
En Elm, il existe trois façons de définir un type, en fonction des besoins.
Tout d'abord, il est possible de définir un alias de type existant :
```elm
type alias MyInt1 = Int -- MyInt1 == Int
type       MyInt2 = Int -- MyInt2 != Int
```
Ici, `MyInt` est "juste" un alias du type `Int`. Il s'agit littéralement du *même* type.
Sans le mot-clé `alias`, on crée un nouveau type, distinct du type `Int`.
Ensuite, il existe les types sommes, à la manière du type `Maybe` vu plus haut.
À titre d'exemple, ce même type `Maybe` est implémenté ainsi :
```elm
type Maybe a
  = Just a
  | Nothing
```
On peut implémenter des énumérations classiques de cette manière :
```elm
type Switch
  = On
  | Off

-- exemple : le type Bool
type Bool
  = True
  | False
```
Mais, les types sommes sont bien plus riches que de bêtes énumérations !
Voyez plutôt :
```elm
-- une donnée de type int, float ou string
type DataType
  = Integer Int
  | Boolean Bool
  | Text    String  
```
Bien entendu, ils peuvent également être utilisés conjointement avec le pattern matching !
```elm
type Operation a
  = Add a a
  | Sub a a
  | Mul a a
  | Div a a

-- et à l'usage :
applyOperation : Operation Int -> Int
applyOperation op =
  case op of
    Add x y -> x + y
    Sub x y -> x - y
    Mul x y -> x * y
    Div x y -> x // y  -- la division d'entiers s'effectue via l'opérateur //

add23 = Add 2 3
five = applyOperation add23

-- on peut évidemment complexifier les choses !
two = applyOperation (Div (applyOperation (Mul 5 2)) (applyOperation (Add 2 3)))
```
Enfin, il existe les "records", très similaires aux `struct`s dans d'autres langages. Par convention, on les déclare en tant que `type alias`.
```elm
type alias User = { nickname : String, email : String }

type alias KeyValue a b = { key : a, value : b }
```

