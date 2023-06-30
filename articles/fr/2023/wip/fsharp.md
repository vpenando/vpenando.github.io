#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Introduction à F#

---

### Introduction

Je discute avec beaucoup de développeurs, et beaucoup aimeraient s'essayer à des langages moins "mainstream" que les habituels C# et autres JavaScript.
Parmi eux, beaucoup *-notamment dans l'écosystème .NET-* aimeraient mener de petits projets en F#.

Bien qu'amateur du langage depuis plusieurs années, je suis loin d'être expert en F#.
Ainsi, cet article se veut être une base pour appréhender la syntaxe du langage, sans pour autant aborder des sujets trop avancés.

---

### Présentation & syntaxe

#### Déclaration d'une variable

En F#, toute variable est typée statiquement, et le type peut bien souvent être déduit par le compilateur :
```ocaml
let inferred = "I'm a string!"

let explicit: string = "I'm also a string!"
```
Par défaut, toute variable est constante !
Le mot-clé [`mutable`](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/values/#mutable-variables) existe cependant, afin de contourner cette restriction, mais il est de préférence à éviter, car l'immuabilité apporte plus d'avantages que d'inconvénients.

#### Déclaration et appel de fonction

Comme pour toute autre variable, le type d'une fonction (et de ses paramètres !) peut être inféré ou explicité :
```ocaml
let inferredFunc param1 param2 =
    // ...

let explicitFunc (param1: string) (param2: string): string =
    // ...
```

Le corps d'une fonction s'écrit juste après le signe `=`, sans besoin d'un `return`.
En effet, une fonction renvoyant toujours quelque chose, ce mot-clé est inutile.
Par ailleurs, il n'est nul besoin d'utiliser des parenthèses lorsque l'on passe des arguments à une fonction.
```ocaml
let add x y = x + y
let five = add 2 3
```
Pour déclarer une variable locale à une fonction, on utilise la même syntaxe que précédemment :
```ocaml
let add x y =
    let sum = x + y
    sum
```

#### Créer de nouveaux types

Il est bien évidemment possible de créer de nouveaux types :
```ocaml
type Point =
    { X: int
    ; Y: int
    }
    
let p: Point = { X = 1; Y = 0 }
```
***Note -** Le type `Point` ci-dessus est un `record`, ce qui n'est pas sans vous rappeler quelque chose si vous êtes adepte du C#.*

Puisque toute variable est immuable, il est impossible de modifier la variable `p`.
À la place, nous créons un nouveau `Point` comportant les modifications voulues via le mot-clé `with` :
```ocaml
let p2 = { p with X = 42 }
```
Il est également possible de [créer des classes](https://learn.microsoft.com/en-us/dotnet/fsharp/language-reference/classes).

F# n'expose pas à proprement parler d'`enum`s, mais utilise à la place des "discriminated unions", qui sont comparables aux `enum`s :
```ocaml
type Switch =
    | On
    | Off

let switchOn = On
```
L'intérêt des "discriminated unions" est que l'on peut associer une ou plusieurs valeurs aux différentes options :
```ocaml
type Shape =
    | Square of float
    | Rectangle of float * float
    | Circle of float

let rectangle = Rectangle(10, 12)
```

#### Gestion des erreurs

Pour la gestion des erreurs, F# utilise les types `Option` et `Result`.

---

### Fonctionnalités spécifiques

#### Application partielle

F# permet l'appel d'une fonction avec une partie seulement des arguments attendus.
En résulte une nouvelle fonction, qui attend les autres :
```ocaml
let add x y = x + y
let add2 = add 2
let five = add2 3
```

#### Chaînage de fonctions

Il est également possible de chaîner des fonctions, c'est à dire passer le résultat d'une fonction en paramètre à une autre fonction.
Cette opération est possible grâce à l'opérateur `|>`, qui passe son premier argument en dernier du second :
```ocaml
let square x = x * x
[1..100] |> List.map square |> printfn "%A"
```
Ainsi, la liste `[1..100]` est passée en dernier argument à la fonction `List.map`, et le résultat de l'appel de cette dernière à `printfn`.
Cela revient à faire successivement :
```ocaml
let inputList = [1..100]
let squareList = List.map square inputList
printfn "%A" squareList
```
Il est bien entendu entendu possible d'en faire une fonction à part entière :
```ocaml
let squareThenPrint inputList =
    inputList |> List.map square |> printfn "%A"
```
Ou plus simplement :
```ocaml
let squareThenPrint =
    List.map square >> printfn "%A"
```
En effet, l'opérateur `>>` rend implicite l'argument `inputList`.

#### Pattern matching

Aujourd'hui disponible dans beaucoup de langages de programmation, le pattern matching existe en F# depuis de nombreuses années.
On l'utilise via les mots-clés `match` / `with` :
```ocaml
type Switch =
    | On
    | Off

let switchToString switch =
    match switch with
    | On -> "On"
    | Off -> "Off"
```
Une version plus simple consiste à utiliser le mot-clé `function`, qui rend implicite l'argument attendu et le `match` / `with` :
```ocaml
type Switch =
    | On
    | Off

let switchToString = function
    | On -> "On"
    | Off -> "Off"
```
Cette version, bien que plus légère, est plus difficile à appréhender pour un débutant.

Le pattern matching en F# est extrêmement puissant, et permet des usages plus complexes, tels que :
```ocaml
(*
    Cet exemple crée une fonction qui sélectionne les premiers N éléments d'une liste.
*)
let rec _takeFirstNItems output n = function (* Le mot-clé 'rec' est nécessaire si la fonction est récursive *)
    | x::xs when n > 0 ->                    (* 'x::xs' décompose une liste en 'x', son premier élément, et 'xs', le reste *)
        let newOutput = output @ [x]         (* L'opérateur @ concatène deux listes *)
        _takeFirstNItems newOutput (n-1) xs  (* On rappelle la fonction avec les éléments restants et n-1 *)

    | _ -> output                            (* Dans tous les autres cas (liste liste vide ou n == 0), on renvoie 'output' *)


let takeFirstNItems = _takeFirstNItems []    (* Application partielle ici ! *)
```
