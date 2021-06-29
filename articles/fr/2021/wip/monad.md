## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Programmation fonctionnelle : Une monade, c'est quoi ?

---

### Définition

Concrètement, le terme monade pourrait être défini ainsi :
> Une monade est une abstraction réunissant un *type paramétré* `M<T>`,
> une fonction `return` (ou `unit`, selon le langage) de signature `T -> M<T>`,
> et une fonction `bind` de signature `M<T> -> (T -> U) -> M<U>`.

***Note -** La fonction `bind` peut également avoir la signature `M<T> -> (T -> M<U>) -> M<U>`.
Bien que le résultat soit formellement identique à la définition ci-dessus, nous utiliserons la première notation.*

En POO, cela pourrait grossièrement être vu comme une interface :
```cs
// Définition
interface Monad<T> {                     // Type paramétré
    Monad<U> Bind<U>(Func<T, U> binder); // Fonction 'bind'
    
    // Le constructeur des classes filles fera office de 'return' / 'unit'
}
```
