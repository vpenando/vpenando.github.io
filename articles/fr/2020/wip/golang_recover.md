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
    std::cout << "erreur : " << e.what();
}
```
Comme attendu, l'erreur sera rattrapée dans le bloc `catch` :
```
erreur : division by zero
```
En Go, la notion d'exception n'existe pas. À la place, il existe la notion d'erreur, qui vient en complément de la possibilité pour une fonction de retourner plusieurs valeurs :
```go
func something() (int, error) {
    // ...
    // du code...
    // ...
    return 0, nil
}
```
Conventionnellement, on utilise la valeur `nil` pour signifier l'absence d'erreur. [Le type `error` étant une interface](https://golang.org/pkg/builtin/#error), il accepte la valeur `nil`.

Parallèlement aux erreurs, il existe la notion de panique, qui se matérialise sous la forme de la fonction `panic()`.

---

### La fonction `panic()`
La fonction `panic()` agit un peu comme une exception ; elle interrompt le flux d'exécution du programme et remonte chacune des fonctions appelantes, jusqu'à-ce que la fonction `recover()` (que nous aborderons plus tard dans cet article) soit appelée. Si cette fonction n'est pas appelée, l'exécution du programme est stoppée et retourne un statut différent de `0`.

Exemple :
```go
func main() {
    panic("une erreur est survenue")
    fmt.Println("ne sera jamais affiché")
}
```
La sortie de ce programme est :
```
panic: une erreur est survenue

goroutine 1 [running]:
main.main()
	chemin/vers/fichier.go:8 +0x40
exit status 2
```




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
