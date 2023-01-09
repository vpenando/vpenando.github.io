#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Comment rendre votre code C# plus sûr
##### (Ou comment se passer d'une valeur `null` et des exceptions)
---

#### Introduction

Qui n'a jamais été agacé de rencontrer à nouveau une `NullReferenceException` ?
C'est certainement le type d'exception le plus récurrent en C#, et par extension le plus insupportable.

Faisant un peu programmation fonctionnelle à côté (plus spécifiquement en [Elm](https://elm-lang.org/)), je me suis surpris à apprécier la façon dont sont gérées les erreurs, à savoir non pas par des exceptions, mais plutôt par l'intermédiaire de types dédiés. En Rust (pour ne citer que lui), il existe les types [`Option`](https://doc.rust-lang.org/std/option/enum.Option.html) et [`Result`](https://doc.rust-lang.org/std/result/enum.Result.html).

Dans cet article, je vous propose une implémentation de ces deux types en C#, ainsi que différents cas d'usage.

### Syntaxe

```c#
record Error(/* ... */);

Result<int, Error> SomethingThatMayFail() {
    if (/* ...*/) {
        return Err(new Error(/* ... */));
    }
    return Ok(42);
}
```
