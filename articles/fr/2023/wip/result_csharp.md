#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Comment rendre votre code C# plus sûr
##### (Ou comment se passer d'une valeur `null` et des exceptions)

---

#### Introduction

Qui n'a jamais été agacé de rencontrer à nouveau une `NullReferenceException` ?
C'est certainement le type d'exception le plus récurrent en C#, et par extension le plus insupportable.

Faisant un peu programmation fonctionnelle à côté (plus spécifiquement en [Elm](https://elm-lang.org/)), je me suis surpris à apprécier la façon dont sont gérées les erreurs, à savoir non pas par des exceptions, mais plutôt par l'intermédiaire de types dédiés. En Rust (pour ne citer que lui), il existe les types [`Option`](https://doc.rust-lang.org/std/option/enum.Option.html) et [`Result`](https://doc.rust-lang.org/std/result/enum.Result.html).

Dans cet article, je vous propose une implémentation de ces deux types en C#, ainsi que différents cas d'usage.

--- 

#### `Result`

Le type `Result` est un type générique, qui peut représenter une valeur *ou* une erreur.
Par ailleurs, il s'agit d'un type à sémantique de valeur, donc il ne doit pas être héritable.
Enfin, il ne doit pas être modifiable car voué à représenter une valeur fixe.

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
    // Note - L'implémentation de 'SendHttpRequest' ne nous intéresse pas ici
    if (!reponse.IsSuccessStatusCode) {
        return Err(/* ... */);
    }
    
    // On traite la réponse ici
    // ...
    
    return Ok(/* ... */);
}
