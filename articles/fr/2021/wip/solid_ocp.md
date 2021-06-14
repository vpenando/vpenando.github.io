## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) SOLID 2/5 - L'OCP

---

### Introduction

```go
type Rectangle struct {
    width  uint
    height uint
}

type Circle struct {
    radius uint
}

func area(shape interface{}) uint {
    switch shape.(type) {
    case Rectangle:
        // ...
    case Circle:
        // ...
    }
    // ...
}
```

```go
type Shape interface {
    area() uint
}
```

