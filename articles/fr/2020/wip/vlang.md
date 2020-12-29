## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) À la découverte du langage V !

---

### Introduction
https://vlang.io/
https://github.com/vlang/v

---


### Syntaxe


```v
fn hello() {
    println("Hello, world!")
}
```

```v
struct Point {
pub mut:
    // x and y are both public and mutable
    x int
    y int
}

// p is immutable
p1 := Point{x: 10, y: 12}
p1.x = 42 // invalid

mut p2 := Point{}
p2.x = 42
p2.y = 12
```
