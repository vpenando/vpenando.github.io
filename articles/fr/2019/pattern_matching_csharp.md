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
  | 42 -> printfn "X vaut 42 !"         // "si x est égal à 42"
  | _  -> printfn "X vaut autre chose"  // "sinon"
  ;;
```
Cette instruction est fortement similaire au `switch` que nous avons dans la plupart des langages, comme ici en C# :
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
        var (lhs, _) when lhs == value => true,
        var (_, rhs) when rhs == value => true,
        _ => false
    };
    
// et à l'usage :
var tuple = (10, 12);
var contains10 = TupleContains(tuple, 10);
```
Equivalent en F# :
```fs
let tuple_contains tup value =
  match tup with
  | (value, _) -> true
  | (_, value) -> true
  | _ -> false
  ;;
```
Plus puissante et plus concise qu'un `switch` habituel (plus de `case`, `break`, ...), l'*expression switch* permet aisément d'effectuer des comparaisons peu triviales auparavant.

Cette syntaxe suit un peu la même logique que le `catch`/`when` introduit par C# 6 (nommé *Exception Filtering*) :
```cs
try {
    // ...
} catch (Exception e) when (<condition1>) {
    // ...
} catch (Exception e) when (<condition2>) {
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

---

### Conclusion
Avec cette future mise à jour (C# 8 est encore en préversion pour le moment), C# tend de plus en plus à se rapprocher de la programmation fonctionnelle, notamment de son cousin F#.

Personnellement, cette orientation me ravit, étant amateur de fonctionnel (bon, j'attends toujours la *const correctness* et les fonctions libres, mais j'espère que ça viendra un jour).

Si vous souhaitez expérimenter ces nouvelles fonctionnalités, je vous invite à télécharger Visual Studio 2019 ou à vous rendre sur [un compilateur en ligne supportant C# 8](https://sharplab.io/).
