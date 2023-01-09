#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Comment rendre votre code C# plus sûr
##### (Ou comment se passer d'une valeur `null` et des exceptions)

---

#### Introduction

Parmi tous les langages que j'utilise, C# est sans aucun doute le langage qui utilise le plus le système d'exceptions, y compris quand cela est le moins judicieux.
À titre d'exemple, `string.Replace` lève une `ArgumentException` si son premier argument est une chaîne vide :
```cs
var newString = oldString.Replace("", "blah");
```
Cette instruction, bien que n'ayant aucun sens, ne devrait a priori juste... ne rien faire.
Lever une exception interrompt le flux d'exécution de notre programme, il ne faut donc *-à mon sens-* les utiliser uniquement lorsque l'on n'a pas d'autre choix.

Par ailleurs, une exception inattendue peut se retrouver `catch`ée dans une couche logicielle où elle n'a plus aucun sens, rendant la phase de debug d'autant plus complexe.

Faisant un peu programmation fonctionnelle sur mon temps libre (plus spécifiquement en [Elm](https://elm-lang.org/)), je me suis surpris à apprécier la façon dont sont gérées les erreurs, à savoir non pas par des exceptions, mais plutôt par l'intermédiaire de types dédiés. En Rust (pour ne citer que lui), il existe les types [`Option`](https://doc.rust-lang.org/std/option/enum.Option.html) et [`Result`](https://doc.rust-lang.org/std/result/enum.Result.html).

Les avantages sont multiples :
- Le flux de notre programme n'est pas interrompu ;
- On sait quelles fonctions / méthodes sont susceptibles d'échouer ;
- Le développeur est incité à traîter l'erreur là où elle se produit, donc dans la couche logicielle concernée.

Dans cet article, je vous propose une implémentation de ces deux types en C#, ainsi que différents cas d'usage.

--- 

#### `Result`

Le type `Result` est un type générique, qui peut représenter une valeur *ou* une erreur.
À cet effet, il expose deux variants : `Ok(T)` et `Err(E)`, représentant respectivement le résultat d'une opération et un cas d'erreur.

Par ailleurs, il s'agit d'un type à sémantique de valeur, donc il ne doit pas être héritable.
Enfin, il ne doit pas être modifiable car voué à représenter une valeur fixe.

Ainsi, il serait proche de :
```cs
readonly record struct Result<T, E>
{
    // ...
}
```

Il doit être manipulable comme suit :

```cs
struct MyValueType {}
struct MyErrorType {}

public Result<MyValueType, MyErrorType> DoSomething() {
    HttpResponseMessage response = SendHttpRequest();
    // L'implémentation de 'SendHttpRequest' ne nous intéresse pas ici
    
    if (!response.IsSuccessStatusCode) {
        return Err(/* ... */);
    }
    
    // ...
    
    return Ok(/* ... */);
}
```

