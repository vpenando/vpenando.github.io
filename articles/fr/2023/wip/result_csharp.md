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

Utiliser ces types plutôt que des exceptions apporte des avantages multiples :
- Le flux de notre programme n'est pas interrompu lorsqu'une erreur est rencontrée ;
- On sait quelles fonctions / méthodes sont susceptibles d'échouer à la lecture de leur signature ;
- Nous sommes incités à traîter l'erreur quand elle se produit, car c'est le seul moment pour le faire.

Dans cet article, je vous propose une implémentation de ces deux types en C#, ainsi que différents cas d'usage.

***Note -** Le code complet des types présentés est disponible [**ici**](https://gist.github.com/vpenando/ac3a2909c0327a5b5030ee82a0de1664).*

## `Result`

#### Présentation
Le type `Result` est un type générique, qui peut représenter une valeur *ou* une erreur.
À cet effet, il expose deux variants : `Ok(T)` et `Err(E)`, représentant respectivement le résultat d'une opération et un cas d'erreur.

Voici son implémentation en Rust :
```rs
enum Result<T, E> {
   Ok(T),
   Err(E),
}

/*
Et à l'usage :
   let ok = Ok(42);
   let err = Err("error");
*/
```

#### Implémentation

Penchons-nous à présent sur sa possible implémentation en C#.

Il s'agit d'un type à sémantique de valeur, donc il ne doit pas être héritable.
De plus, il ne doit pas être modifiable car voué à représenter une valeur fixe.

Ainsi, il serait proche de :
```cs
readonly record struct Result<T, E>
{
    // ...
}
```

Il doit être instanciable comme suit :

```cs
Result<T, E> okResult = Ok(value);
Result<T, E> errResult = Err(err);
```
Se pose alors un problème : que se passe-t-il si on utilise l'inférence de type ?
```cs
var okResult = Ok(value); // Comment inférer le type E ici ?
var errResult = Err(err); // Comment inférer le type T ici ?
```
La solution semble évidente : il faut simplement utiliser un type intermédiaire, à partir duquel on instanciera notre `Result<T, E>`.
Voici la version "Ok" :
```cs
public readonly record struct Ok<T>
{
    public readonly T Value { get; }

    public Ok(T value)
    {
        this.Value = value;
    }
}
```
Et son équivalent pour "Err" :
```cs

public readonly record struct Err<E>
{
    public readonly E Error { get; }

    public Err(E error)
    {
        this.Error = error;
    }
}
```
Mais ce n'est pas tout, il nous faut ensuite modifier le type `Result`, afin de pouvoir l'instancier implicitement à partir d'un `Ok<T>` ou d'un `Err<E>` :
```cs
public readonly record struct Result<T, E>
{
    private Result(Ok<T> ok)
    {
        // ...
    }

    private Result(Err<E> err)
    {
        // ...
    }

    public static implicit operator Result<T, E>(Ok<T> ok)
        => new(ok);

    public static implicit operator Result<T, E>(Err<E> err)
        => new(err);
}
```
L'implémentation complète pourrait donc être proche de :
```cs
public readonly record struct Result<T, E>
{
    private readonly T value = default!;
    private readonly E error = default!;
    private readonly ResultType type;

    public T Value
        => this.IsOk() ? this.value : throw new InvalidOperationException($"No value in result '{this}'");

    public E Error
        => this.IsErr() ? this.error : throw new InvalidOperationException($"No error in result '{this}'");

    private Result(Ok<T> ok)
    {
        this.value = ok.Value;
        this.type = ResultType.Ok;
    }

    private Result(Err<E> err)
    {
        this.error = err.Error;
        this.type = ResultType.Err;
    }

    public static implicit operator Result<T, E>(Ok<T> ok)
        => new(ok);

    public static implicit operator Result<T, E>(Err<E> err)
        => new(err);

    public bool IsOk()
        => this.type == ResultType.Ok;

    public bool IsErr()
        => this.type == ResultType.Err;

    private enum ResultType
    {
        Ok,
        Err
    }
}
```
Enfin, il reste une dernière chose pour pouvoir utiliser `Ok` et `Err` comme présenté plus haut :
```cs
public static class ResultModule
{
    public static Ok<T> Ok<T>(T value)
        => new(value);

    public static Err<E> Err<E>(E error)
        => new(error);
}
```

#### À l'usage

Ce faisant, il nous suffit d'utiliser `using static ResultModule;` afin d'avoir accès à `Ok` et `Err` :
```cs
using static Result;

var ok = Ok(42);
var err = Err("Error :(");

Result<int, string> DoSomething(bool ok)
   => ok ? Ok(42) : Err("Error :(");
```
Mieux encore, on peut l'utiliser via un [`global using`](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/using-directive#global-modifier) !

***Note -** Le code complet du type `Result` est disponible [**ici**](https://gist.github.com/vpenando/ac3a2909c0327a5b5030ee82a0de1664).*

