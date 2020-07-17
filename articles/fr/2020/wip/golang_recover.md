## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) La fonction `recover()` en Go

---

### Sommaire
* [Introduction](#introduction)
* [La fonction `panic()`](#la-fonction-panic)
* [Point sur `defer`](#point-sur-defer)
* [La fonction `recover()`](#la-fonction-recover)
* [Conclusion](#conclusion)

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
La fonction `panic()` agit un peu comme une exception ; elle interrompt le flux d'exécution du programme et remonte chacune des fonctions appelantes, jusqu'à rencontrer un appel à la fonction `recover()`, que nous aborderons plus tard dans cet article. Si cette fonction n'est pas appelée, l'exécution du programme finit par être stoppée et celui-ci retourne un statut différent de `0`. Ainsi, appeler `panic()` signifie que notre programme ne **doit** pas continuer normalement, contrairement à un retour d'erreur.

```go
func main() {
    callPanic()
    fmt.Println("will never be reached")
}

func callPanic() {
    panic("something bad happened :(")
}
```
([Source](https://play.golang.org/p/SGvkXHtjpbi))

La sortie de ce programme est :
```
panic: something bad happened :(

goroutine 1 [running]:
main.main()
    chemin/vers/fichier.go:ligne
exit status 2
```
Le programme est terminé avec un code d'erreur et la call stack afin de remonter jusqu'à la source de notre erreur.

Il fait donc sens de l'appeler lorsque le programme est dans un état incohérent et qu'il ne doit plus poursuivre son flux d'exécution normalement.
Néanmoins, il existe une petite subtilité dans le cas de l'utilisation du mot-clé `defer`.

---

### Point sur `defer`
En Go, le mot-clé `defer` permet de déléguer un appel à la fin d'une fonction donnée.

Exemple :
```go
func main() {
    defer func() {
        fmt.Println("last :(")
    }()
    fmt.Println("first!")
}
```
Ici, la fonction anonyme sera appelée à la fin de `main`. ([Source](https://play.golang.org/p/kd-onsT1Qp-))

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
    fmt.Println("will never be reached :(")
}

func caller() {
    defer func() {
        fmt.Println("third!")
    }()
    called()
}

func called() {
    defer func() {
        fmt.Println("second!")
    }()
    defer func() {
        fmt.Println("first!")
    }()
    panic("something bad happened")
}
```
Output :
```
first!
second!
third!
panic: something bad happened

goroutine 1 [running]:
main.called()
    chemin/vers/fichier.go:ligne
main.caller()
    chemin/vers/fichier.go:ligne
main.main()
    chemin/vers/fichier.go:ligne
exit status 2
```
([Source](https://play.golang.org/p/tof87famHnH))

Ce point est crucial lors de la gestion des appels à `panic()`, car, vous vous en doutez, on veut parfois pouvoir reprendre le contrôle d'un programme plutôt que de le faire crasher.

---

### La fonction `recover()`
En Go, la fonction `recover()` permet de rétablir le contrôle de notre programme si un appel à `panic()` a été effectué. Elle permet également de récupérer la valeur passée en paramètre de `panic()`.

Néanmoins, `panic()` rompt le flux d'exécution du programme. Ainsi, le code suivant n'aura pas le résultat escompté :
```go
func tryRecover() {
    panic("aaaah!") // rompt l'exécution
    recover()       // n'aura aucun effet
}

func main() {
    tryRecover()
}
```
L'appel à `recover()` ne sera **jamais** atteint dans ce cas. Et c'est là qu'intervient `defer` ; en effet, un appel "deferred" sera exécuté même en cas de `panic()` ! Ainsi, le code suivant fonctionnera comme attendu :
```go
func tryRecover() {
    defer func() {
        if err := recover(); err != nil {
            fmt.Println("the error was:", err)
        }
    }()
    panic("aaaah!")
}

func main() {
    tryRecover()
    fmt.Println("ok!") // sera bien exécuté
}
```
([Source](https://play.golang.org/p/vQeKR9d0vwA))

Néanmoins, la plupart des erreurs rencontrées devrait à mon sens être traitée par valeurs de retour. Les appels à `panic()` doivent être utilisés avec parcimonie, car peuvent théoriquement stopper net le programme. Ils témoignent d'un comportement ne permettant pas de continuer le fonctionnement normal.

---

### Conclusion
Go dispose de deux mécanismes pour traiter les comportement anormaux ; les erreurs, que l'on utilise conventionnellement comme valeurs de retour, et la fonction `panic()`, qui agit un peu comme une exception dans d'autres langages. Parallèlement, la fonction `recover()` permet de rattraper l'erreur, comme une clause `catch` éviterait qu'une exception stoppe notre programme.

Ainsi, tant que le comportement de notre programme n'est pas fatal, je préconise de simplement renvoyer des erreurs. A contrario, si notre programme ne peut (et ne doit) pas continuer, alors il convient de l'arrêter via `panic()`.
