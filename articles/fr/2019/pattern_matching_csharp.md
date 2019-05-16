## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Du pattern matching en C# ! (C# 8)

---

### Sommaire
* Introduction


---

### Introduction
En programmation fonctionnelle, il existe la notion de pattern matching :
```fs
// ici en F#
let match_with_42 x =
  match x with
  | 42 -> // ...
  | _  -> // ...
  ;;
```

Cette instruction est fortement similaire au `switch` que nous avons dans la plupart des langages :
```cs
void MatchWith42(int x) {
    switch (x) {
    case 42:
        // ...
        break;
    default:
        // ...
    }
}
```
Néanmoins, contrairement au `switch` que nous connaissons, le pattern matching permet de décomposer des entités, comme par exemple :
```fs
// cette fonction renvoie une valeur optionnelle :
// 'Some X' ou 'None'
let head_of_list ls =
  match ls with
  | []   -> None    // si la liste est vide, on renvoie une valeur nulle
  | x::_ -> Some x  // sinon, on renvoie son premier élément, ici x
  ;;
```

---


### Application au C#
C# 8 a introduit une forme de pattern matching, en suivant la syntaxe suivante :
```cs
variableToTest switch {
    value1 [when condition1] => result1,
    value2 [when condition2] => result2,
    // ...
    _ => defaultResult
}
```
Exemple :
```cs
bool TupleContains((object, object) tuple, object value)
    => tuple switch {
        var (x, _) when object.Equals(x, value) => true,
        var (_, y) when object.Equals(y, value) => true,
        _ => false
    };
```
Equivalent en F# :
```fs
let tuple_contains tup value =
  match tup with
  | (value, _) -> true
  | (_, value) -> true
  | _ -> false
```

Cette syntaxe suit un peu la même logique que le `catch`/`when` introduit par C# 6 (nommé *Exception Filtering*) :
```cs
try {
    // ...
} catch (Exception e) when (<condition>) {
    // ...
}
```
Exemple :
```cs
void DoSomething(int a, int b, bool catchExceptionIfAny = true) {
    try {
        // ...
        throw new Exception("Catch me if you can!");
    } catch (Exception e) when (catchExceptionIfAny) {
        Console.WriteLine("Got ya!");
    }
}
```
