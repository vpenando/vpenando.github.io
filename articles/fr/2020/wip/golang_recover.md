## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Recover en Go

---

### Sommaire

---

### Introduction

* Fonction`defer`red appelée après `panic`
```go
package main

import (
    "fmt"
    "log"
)

func main() {
    divideByZero()
    fmt.Println("we survived dividing by zero!")
}

func divideByZero() {
    defer func() {
        if err := recover(); err != nil {
            log.Println("panic occurred:", err)
        }
    }()
    fmt.Println(divide(1, 0))
}

func divide(a, b int) int {
    if b == 0 {
        panic(nil)
    }
    return a / b
}
```
