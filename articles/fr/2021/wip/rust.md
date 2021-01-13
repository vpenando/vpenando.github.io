## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) 2021, l'année de Rust ?

---

### Introduction

Créé par Mozilla, Rust est un langage de programmation orienté système, proposant également quelques aspects fonctionnels.
Il s'agit d'un langage extrêmement populaire ces derniers temps, notamment grâce à sa sûreté.

Rust est souvent opposé à Go ; personnellement je n'ai aucun parti pris et apprécie les deux langages.

---

### Présentation de Rust

```rust
fn main() {
  println!("Hello, world!");
}
```
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
  let a = 42; // 'a' est seul propriétaire de sa valeur, ici 42.
  // ...
}             // fin du scope : 'a' est détruite.
```

Parallèlement, bien qu'il n'existe pas de classes et d'héritage en Rust, ce dernier propose la notion de *trait*, semblable aux interfaces dans d'autres langages :
```rust
trait Speaker {
  fn speak(&self);
}
```

---

### Une popularité croissante

---

### Conclusion
