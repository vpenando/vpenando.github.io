## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Recover en Go

---

### Sommaire

---

### Introduction
Dans de nombreux langages, la gestion des erreurs se fait via des exceptions. Exemple en C++ :
```cpp
int divide(int a, int b) {
    if (b == 0) {
        throw std::runtime_error("division by zero");
    }
    return a / b;
}
```
Si nous tentons d'appeler notre fonction `divide` avec comme second argument `0`, une exception sera levée. Cette exception rompt le flux d'exécution du programme et remonte chacune des fonctions appelantes jusqu'à rencontrer un bloc `try` duquel l'une des fonctions appelantes aurait été appelée. Elle sera ensuite rattrappée par la clause `catch`. Illustration :
```cpp
try {
    int result = divide(1, 0);
} catch (std::exception const& e) {
    // l'exception sera rattrappée dans ce bloc
}
```

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

---

### `panic()`
* Stoppe appelle chaque fonction `defer`red, puis retourne de la fonction courante, et ainsi de suite pour chaque fonction
* Termine le programme avec un code != 0



---

### Point sur `defer`
* Délègue un appel à la fin de la fonction avant son `return`
* Empile des appels à des goroutines

---

### `recover()`
* Rétablit le contrôle du programme ; interrompt un `panic()`
* Inutile en dehors d'une goroutine `defer`red
