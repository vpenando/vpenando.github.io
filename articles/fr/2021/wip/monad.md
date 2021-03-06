## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Programmation fonctionnelle : Une monade, c'est quoi ?

---

### Définition

Concrètement, le terme monade pourrait être défini ainsi :
> Une monade est une abstraction réunissant :
> - un *type paramétré* `M<T>`,
> - une fonction `return` (ou `unit`, selon le langage) de signature `T -> M<T>`,
> - une fonction `bind` de signature `M<T> -> (T -> M<U>) -> M<U>`.

En POO, cela pourrait grossièrement être vu comme une interface :
```cs
interface Monad<T> {                            // Type paramétré
    Monad<U> Bind<U>(Func<T, Monad<U>> binder); // Fonction 'bind'
    
    // Le constructeur des classes filles fera office de 'return' / 'unit'
}
```
Notons toutefois que cette représentation, très maladroite, est là uniquement à but illustratif.

En somme, une monade est une sorte de boite noire, pouvant encapsuler une valeur.
Lui appliquer la fonction `bind` renvoie une nouvelle valeur monadique.

```rust
// Exemple en pseudo-code

type MyMonad<T> {
    T value;
    
    unit(T value)
        => MyMonad<T>(value);
    
    bind(Func<T, MyMonad<U>> binder, MyMonad<T> input)
        => binder(input.value);
}

let m1 = MyMonad.return(42);                                 // m1.value == 42
let m2 = MyMonad.bind(x => MyMonad.return(toString(x)), m1); // m2.value == "42"
```
---

### Mise en pratique

En F#, il existe le type `Option`, encapsulant une valeur optionnelle.
Et devinez quoi ? `Option` a toutes les caractéritiques basiques d'une monade !

En effet, c'est un type paramétré, exposant un constructeur de type et une fonction `bind` !
```ocaml
let myOptionalString = Some("Hello")
let bound = Option.bind (fun s -> Some(s + ", world!")) myOptionalString

match bound with
    | Some(s) -> printfn "%s" s
    | None    -> printfn "None"
```
