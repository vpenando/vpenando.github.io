#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Introduction à F#

---

#### Introduction


---

#### Présentation & syntaxe


##### Déclaration d'une variable

En F#, toute variable est typée statiquement, et le type peut bien souvent être déduit par le compilateur :
```ocaml
let inferred = "I'm a string!"

let explicit: string = "I'm also a string!"
```
Par défaut, toute variable est constante !
Le mot-clé `mutable` existe cependant, afin de contourner cette restriction, mais il est de préférence à éviter, car l'immuabilité apporte plus d'avantages que d'inconvénients.

##### Déclaration et appel de fonction

Toute comme les variables, le type d'une fonction et de ses paramètres peut être inféré ou explicité :
```ocaml
let inferredFunc param1 param2 =
    // ...

let explicitFunc (param1: string) (param2: string): string =
    // ...
```

Le corps d'une fonction s'écrit juste après le signe `=`, sans besoin d'un `return`.
En effet, une fonction renvoyant toujours quelque chose, ce mot-clé est inutile.
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

##### Créer de nouveaux types

```ocaml
type Point =
    { X: int
    ; Y: int
    }
    
let p: Point = { X = 1; Y = 0 }
let p2 = { p with X = 42 }
```

```ocaml
type Switch =
    | On
    | Off
```

---

#### Fonctionnalités spécifiques

##### Application partielle

##### Pattern matching
