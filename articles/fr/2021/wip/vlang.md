## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) À la découverte du langage V !

---

### Introduction
https://vlang.io/
https://github.com/vlang/v

---


### Syntaxe
Syntaxiquement, V est à mi-chemin entre Go et Rust. 

#### Types primitifs

```v
bool
string
i8    i16  int  i64
byte  u16  u32  u64
rune
f32 f64
any_int, any_float
byteptr, voidptr, charptr, size_t
any
```

#### Fonctions
```v
fn hello() {
    println("Hello, world!")
}

fn add(a int, b int) int {
    return a + b
}
```

#### Structures
```v
struct Point {
pub mut:
    // x et y sont publics et mutables
    x int
    y int
}

// p n'est PAS mutable !
p1 := Point{x: 10, y: 12}
p1.x = 42 // invalide !

mut p2 := Point{}
p2.x = 42
p2.y = 12
```
