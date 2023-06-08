#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Introduction à F#

---

#### Introduction


---

#### Présentation & syntaxe


##### Déclaration d'une variable
```fs
let inferred = "I'm a string!"

let explicit: string = "I'm also a string!"
```
Toute variable est constante !

##### Déclaration et appel de fonction
```fs
let inferredFunc param1 param2 =
    // ...


let explicitFunc (param1: string) (param2: string): string =
    // ...
```

```fs
let add x y = x + y // Déclaration d'une fonction 'add'
let five = add 2 3  // Appel de la fonction
```

##### Créer de nouveaux types

```fs
type Point =
    { X: int
    ; Y: int
    }
    
let p: Point = { X = 1; Y = 0 }
let p2 = { p with X = 42 }
```

```fs
type Switch =
    | On
    | Off
```

---

#### Fonctionnalités spécifiques

##### Application partielle

##### Pattern matching
