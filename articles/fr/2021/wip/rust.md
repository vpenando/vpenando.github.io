## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) 2021, l'année de Rust ?

---

### Introduction

Créé par Mozilla, Rust est un langage de programmation orienté système, proposant également quelques aspects fonctionnels.
Il s'agit d'un langage extrêmement populaire ces derniers temps, notamment grâce à sa sûreté.

Rust est souvent opposé à Go ; personnellement je n'ai aucun parti pris et apprécie les deux langages.

Au cours de cet article, nous présenterons brièvement Rust et aborderons les raisons de sa popularité croissante.

---

### Présentation de Rust
Abordons sa syntaxe avec un traditionnel hello world :
```rust
fn main() {
  println!("Hello, world!");
}
```
On remarque que l'on déclare une fonction grâce au mot-clé `fn`. Pour afficher du texte, on utilise la *macro* `println`. Pour le moment, considérez qu'une macro est similaire à une fonction, à ceci près qu'il faut la suffixer d'un point d'exclamation pour l'appeler.

Rust propose des mécanismes communs à de nombreux langages (tels que les stuctures, les énumérations, etc...), mais également d'autres outils, comme le pattern matching, ou encore les types sommes ! De plus, toute variable est par défaut constante mais peut être déclarée mutable grâce au mot-clé `mut`.

Exemple :
```rust
let a = 42;     // 'a' est constante.
let mut b = 12; // 'b' est mutable.
```

Toutefois, la spécificité la plus connue de Rust est son système d'*ownership*. Le principe d'*ownership* en Rust est fondamental et énonce trois règles cruciales. Pour citer [la doc](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html) :
> * Each value in Rust has a variable that’s called its *owner*.
> * There can only be one owner at a time.
> * When the owner goes out of scope, the value will be dropped.

En somme, une valeur est associée à un propriétaire, et sa durée de vie dépend de celle de ce dernier. Imaginons le programme suivant :
```rust
fn main() {
  let a = 42;            // 'a' est seul propriétaire de sa valeur, ici 42.
  println!("a = {}", a); // on fait des super trucs
}                        // fin du scope : 'a' est détruite.
```
On peut également créer des structures grâce au mot-clé `struct`. Il est même possible de leur créer des méthodes ! Génial, non ?
```rust
struct Foo {
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
Pour implémenter les méthodes d'une structure, il est nécessaire de se situer dans un bloc `impl`. Personnellement, je trouve cela très verbeux, en plus de créer un niveau d'imbrication superflu. Cela a néanmoins l'intérêt de savoir clairement quel type nous implémentons. Tout comme en Python, les méthodes non-statiques prennent un premier argument, `self`.

> Que sont ces `&` présents devant `self` ?

Bonne question ! Vous vous rappelez de la notion d'*ownership* ? Chaque valeur a *un et un seul* propriétaire ; ainsi, Rust utilise la sémantique de mouvement pour assurer cela. Toute variable transmise à une fonction est déplacée et ne sera alors *plus utilisable*, à la manière de `std::move` en C++. Or, on ne veut pas que notre instance soit déplacée, car on peut en avoir encore besoin après ! Pour ce faire, on *emprunte* la valeur de `self` ; il s'agit de la notion de *borrowing*. Pour ce faire, on utilise des références. Tout comme en C++, une référence est matérialisée par le symbole `&`.


Parallèlement, bien qu'il n'existe pas de classes et d'héritage en Rust, ce dernier propose la notion de *trait*, semblable aux interfaces dans d'autres langages :
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

impl Cat for Speaker
  fn speak(&self) {
  }
}
```

---

### Une popularité croissante

---

### Conclusion
