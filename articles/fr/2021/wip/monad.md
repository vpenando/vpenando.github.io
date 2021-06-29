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
interface Monad<T> {                     // Type paramétré
    Monad<U> Bind<U>(Func<T, U> binder); // Fonction 'bind'
    
    // Le constructeur des classes filles fera office de 'return' / 'unit'
}
```
Notons toutefois que cette représentation, très maladroite, est là uniquement à but illustratif.

En somme, une monade est une sorte de boite noire, pouvant encapsuler une valeur.
Lui appliquer la fonction `bind` renvoie une nouvelle valeur monadique.

```fs
// Exemple en pseudo-code

type MyMonad<T> {
    T value;
    
    unit(T value) => MyMonad<T>(value);
    bind(Func<T, U> binder, MyMonad<T> input) => MyMonad<U>(binder(input.value));
}

let m1 = MyMonad<int>.unit(42);                   // m1.value == 42
let m2 = MyMonad<int>.bind(x => toString(x), m1); // m2.value == "42"
```
---

### Mise en pratique

```ml
// Exemple avec une monade régissant une valeur optionnelle

// Type monadique M<T>
type Option<'T> =
    | Some of 'T
    | None
     
// Fonction 'return' / 'unit'
let unit v = Some(v)

// Fonction 'bind'
let bind func option =
    match option with
        | Some(x) -> Some(func x )
        | None    -> None
```
Et à l'usage :
```fs
let myOptionalString = Some("Hello")
let bound = bind (fun s -> s + ", world!") myOptionalString

match bound with
    | Some(s) -> printfn "%s" s
    | None    -> printfn "None"
```
