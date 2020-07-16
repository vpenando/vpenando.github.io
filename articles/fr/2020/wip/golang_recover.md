## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Recover en Go

---

### Sommaire

---

### Introduction
Dans de nombreux langages, la gestion des erreurs se fait via des exceptions.

Exemple en C++ :
```cpp
int divide(int n, int divisor) {
    if (divisor == 0) {
        throw std::runtime_error("division by zero");
    }
    return n / divisor;
}
```
Si nous tentons d'appeler notre fonction `divide` avec comme second argument `0`, une exception sera levée. Cette exception rompt le flux d'exécution normal du programme et remonte chacune des fonctions appelantes jusqu'à rencontrer un bloc `try` duquel l'une de ces fonctions a été appelée. Elle sera ensuite rattrappée par la clause `catch`. 

Illustration :
```cpp
try {
    int result = divide(1, 0);
} catch (std::exception const& e) {
    // l'exception sera rattrappée dans ce bloc
    std::cout << "erreur : " << e.what();
}
```
([Source](http://coliru.stacked-crooked.com/a/d06febe1057c5d33))

Comme attendu, l'exception sera rattrapée dans le bloc `catch` et l'output sera bien évidemment :
```
error: division by zero
```
En Go, la notion d'exception n'existe pas. À la place, il existe la notion d'erreur, qui vient en complément de la possibilité pour une fonction de retourner plusieurs valeurs :
```go
func something() (int, error) {
    // ...
    // du code...
    // ...
    return 0, errors.New("something bad happened :(")
}
```
Il est courant pour une fonction de retourner une valeur supplémentaire, afin de renseigner la possible rencontre d'une erreur.
Conventionnellement, on utilise la valeur `nil` pour signifier l'absence d'erreur. [Le type `error` étant une interface](https://golang.org/pkg/builtin/#error), il accepte la valeur `nil`.

Parallèlement aux erreurs, il existe la notion de panique, qui se matérialise sous la forme de la fonction `panic()`.

---

### La fonction `panic()`
La fonction `panic()` agit un peu comme une exception ; elle interrompt le flux d'exécution du programme et remonte chacune des fonctions appelantes, jusqu'à rencontrer un appel à la fonction `recover()`, que nous aborderons plus tard dans cet article. Si cette fonction n'est pas appelée, l'exécution du programme finit par être stoppée et celui-ci retourne un statut différent de `0`.

```go
func main() {
    callPanic()
    fmt.Println("ne sera jamais affiché")
}

func callPanic() {
    panic("une erreur est survenue")
}
```
([Source](https://play.golang.org/p/HeTJoJtH5TN))

La sortie de ce programme est :
```
panic: une erreur est survenue

goroutine 1 [running]:
main.main()
    chemin/vers/fichier.go:ligne
exit status 2
```
Il fait donc sens de l'appeler lorsque le programme est dans un état incohérent et qu'il ne doit plus poursuivre son flux d'exécution normalement.
Néanmoins, il existe une petite subtilité dans le cas de l'utilisation du mot-clé `defer`.

---

### Point sur `defer`
En Go, le mot-clé `defer` permet de déléguer un appel à la fin d'une fonction donnée.

Exemple :
```go
func main() {
    defer func() {
        fmt.Println("sera affiché en dernier")
    }()
    fmt.Println("sera affiché en premier")
}
```
Ici, la fonction anonyme sera appelée à la fin de `main`. ([Source](https://play.golang.org/p/kQRWpuT1bqD))

Ce mécanisme est par exemple utilisé lorsque l'on manipule des fichiers :
```go
file, err := os.Open("test.txt")
if err != nil {
    // traitement de l'erreur
}
defer file.Close()
// ...
// du code...
// ...
```
L'appel "deferred" est empilé et, lorsque l'on atteint la fin de la fonction courante, tous les appels empilés via `defer` sont successivement exécutés.
La particularité de `defer` est que les appels empilés seront exécutés même en cas de `panic()` :
```go
func main() {
    caller()
    fmt.Println("ne sera jamais affiché :(")
}

func caller() {
    defer func() {
        fmt.Println("sera affiché en troisième !")
    }()
    called()
}

func called() {
    defer func() {
        fmt.Println("sera affiché en deuxième !")
    }()
    defer func() {
        fmt.Println("sera affiché en premier !")
    }()
    panic("panique !")
}
```
Output :
```
sera affiché en premier !
sera affiché en deuxième !
sera affiché en troisième !
panic: panique !

goroutine 1 [running]:
main.called()
    chemin/vers/fichier.go:ligne
main.caller()
    chemin/vers/fichier.go:ligne
main.main()
    chemin/vers/fichier.go:ligne
exit status 2
```
([Source](https://play.golang.org/p/6PCBN0_U9J5))

Ce point est crucial lors de la gestion des appels à `panic()`.

---

### La fonction `recover()`
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
