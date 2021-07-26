## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Introduction à Rust

---

### Introduction

Soutenu par Mozilla, Rust est un langage de programmation orienté système, proposant également quelques aspects fonctionnels.

Langage [préféré des développeurs en 2020](https://insights.stackoverflow.com/survey/2020#technology-most-loved-dreaded-and-wanted-languages-loved) sur Stack Overflow ainsi [qu'en 2019](https://insights.stackoverflow.com/survey/2019#technology-_-most-loved-dreaded-and-wanted-languages), Rust bénéficie d'une popularité de plus en plus importante au sein des différentes communautés.

De nombreuses entreprises commencent à l'adopter, et l'intérêt croissant porté sur Rust par des mastodontes tels qu'Amazon, Google et Microsoft contribue à fédérer les développeurs autour du langage. Il se veut être un remplaçant de C dans des domaines tels que la programmation système, offrant des performances presque équivalentes tout en s'affranchissant de la gestion manuelle de la mémoire.

Rust est souvent opposé à Go ; personnellement je n'ai aucun parti pris et apprécie les deux langages.

Au cours de cet article, nous présenterons brièvement Rust et nous pencherons sur les raisons de sa popularité croissante.

---

### Présentation de Rust
Abordons sa syntaxe avec un traditionnel hello world :
```rust
fn main() {
    println!("Hello, world!");
}
```
On remarque que tout programme Rust a, comme en C ou en C++, comme point d'entrée la fonction `main`, et que l'on déclare une fonction grâce au mot-clé `fn`. Pour afficher du texte, on utilise la *macro* `println`. Pour le moment, considérez qu'une macro est similaire à une fonction, à ceci près qu'il faut la suffixer d'un point d'exclamation pour l'appeler.

Rust propose des mécanismes communs à de nombreux langages (tels que les stuctures, les énumérations, etc...), mais également d'autres outils, comme le pattern matching, ou encore les types sommes ! De plus, toute variable est par défaut constante mais peut être déclarée mutable grâce au mot-clé `mut`.

Exemple :
```rust
let a = 42;     // 'a' est constante.
let mut b = 12; // 'b' est mutable.
```

De plus, la spécificité la plus connue de Rust est son système d'*ownership*. Le principe d'*ownership* en Rust est fondamental et énonce trois règles cruciales. Pour citer [la doc](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html) :
> * Each value in Rust has a variable that’s called its *owner*.
> * There can only be one owner at a time.
> * When the owner goes out of scope, the value will be dropped.

En somme, une valeur est associée à un propriétaire, et sa durée de vie dépend de celle de ce dernier. Imaginons le programme suivant :
```rust
fn main() {
    let a = 42;            // 'a' est seul propriétaire de sa valeur, ici 42.
    println!("a = {}", a); // on fait des super trucs
}                          // fin du scope : 'a' est détruite.
```
On peut également créer des structures grâce au mot-clé `struct`. Il est même possible de leur créer des méthodes ! Génial, non ?
```rust
struct Foo {
    i: i32,     // membre 'i', privé (= inaccessible en dehors du module)
    pub j: f32, // membre 'j', public
}

impl Foo {
    fn bar(&self) {
        // ...
    }
  
    fn baz(&self) {
        // ...
    }
}
```
Pour implémenter les méthodes d'une structure, il est nécessaire de se situer dans un bloc `impl`. Tout comme en Python, les méthodes non statiques prennent un premier argument, `self`.

> Que sont ces `&` présents devant `self` ?

Bonne question ! Vous vous rappelez de la notion d'*ownership* ? Chaque valeur a *un et un seul* propriétaire ; ainsi, Rust utilise la sémantique de mouvement pour assurer cela. Toute variable transmise à une fonction est déplacée et ne sera alors *plus utilisable*, à la manière de `std::move` en C++. Or, on ne veut pas que notre instance soit déplacée, car on peut en avoir encore besoin après ! Pour ce faire, on *emprunte* la valeur de `self` ; il s'agit de la notion de *borrowing*, qui se matérialise par le biais de l'utilisation de références. Tout comme en C++, une référence est symbolisée par le caractère `&`.

Parallèlement, bien qu'il n'existe pas de classes en Rust, ce dernier propose la notion de *trait*, semblable aux interfaces dans d'autres langages :
```rust
trait Speaker {
    fn speak(&self);
}
```
Ainsi, il est possible de profiter du polymorphisme au sein d'un programme Rust :
```rust
trait Speaker {
    fn speak(&self);
}

struct Cat {
}

impl Speaker for Cat { // notez la syntaxe différente du 'impl' !
    fn speak(&self) {
        println!("meow");
    }
}
```
Et à l'usage :
```rust
// on attend un type T qui implémente Speaker
fn make_speak<T: Speaker>(speaker: &T) {
    speaker.speak();
}
```
Notons qu'un trait peut proposer une implémentation par défaut des méthodes qu'il contient.

Résumons toutes ces notions avec un code simple :
```rust
// on crée un trait Speaker comme plus haut
trait Speaker {
    fn speak(&self);
}

// on crée deux structures qui implémenteront
// ce trait
struct Cat {}
struct Dog {}

// implémentation de Speaker
impl Speaker for Cat {
    fn speak(&self) {
        println!("meow");
    }
}

// implémentation de Speaker
impl Speaker for Dog {
    fn speak(&self) {
        println!("woof");
    }
}

// une fonction attendant un Speaker, peu importe
// le type concret
fn make_speak<T: Speaker>(speaker: &T) {
    speaker.speak();
}

fn main() {
    let cat = Cat{};     // on crée une variable constante
    let mut dog = Dog{}; // on crée une variable mutable
    make_speak(&cat);    // on appelle notre fonction 'make_speak'
    make_speak(&dog);    // idem
}
```
Ce code, très simple, permet de mettre en pratique les différents points abordés au cours de cette section.

---

### Conclusion
Il existe bien d'autres concepts que nous n'avons pas présentés ici, tels que les modules, la gestion des dépendances, le code `unsafe` ou encore l'asynchronisme avec `async`/`await`. Cela fera très certainement l'objet de prochains articles !

En attendant, je ne peux que vous orienter vers [la documentation officielle](https://www.rust-lang.org/learn), qui est bien structurée et très bien écrite.
