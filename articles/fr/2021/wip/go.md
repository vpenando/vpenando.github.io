## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Retour d'expérience sur Go

---

### Introduction
Après quelques années de pratique sur Go, et quasi quotidiennement ces derniers mois, je souhaiterais faire un petit retour d'expérience sur Go ;
aborder ses points forts, ses points faibles, et dresser un modeste bilan sur mon utilisation de ce langage.
Nous n'entrerons pas dans les détails de chacun des différents points abordés, aussi n'hésitez pas à mener vos propres recherches si vous souhaitez approfondir vos connaissances sur l'un (ou plusieurs !) des différents sujets abordés.

---

### Points forts de Go
Avant tout, quels sont les points forts de Go ? Ces derniers ne manquent pas, et sont pour certains plus que significatifs.

#### 1. Go, un langage compilé
Go est non seulement un langage compilé (à différencier de langages *interprétés*, comme la plupart des langages de script par exemple).
En tant que tel, il apporte certaines vérifications au *compile time*, permettant ainsi d'offrir des garanties supplémentaires plutôt que de planter de manière inattendue à l'exécution.
De plus, étant compilé en langage machine, il offre des performances presque comparables à du C ou du C++.
La compilation d'un programme Go est quasiment instantanée ; ce faisant, certains utilisent Go comme langage de script en lieu et place de Bash ou Python via la commande `go run`.

Enfin, en jouant avec les variables d'environnement `GOOS` et `GOARCH`, [il est possible de compiler vers un OS différent de la machine courante](https://www.digitalocean.com/community/tutorials/building-go-applications-for-different-operating-systems-and-architectures) !

#### 2. Go, un langage statiquement typé
Go est dit statiquement typé. Cela signifie qu'une variable a *un et un seul* type.
Il n'est théoriquement (\*) pas possible d'assigner à une même variable des valeurs de différents types.
Ainsi, pas de risque de surprise à l'évaluation d'une variable.

*(**\***) Il existe une exception, le type `interface{}`, détaillée dans la partie "points faibles de Go".*

#### 3. Une syntaxe simple et lisible
Go a une syntaxe à la fois simple et très lisible.
La lecture d'un code Go est extrêmement fluide et sa compréhension se fait sans aucune difficulté.
À titre personnel, je n'ai jamais eu aucun problème à comprendre le code source de langage ou de certaines bibliothèques tierces lorsque j'en ai eu besoin.
Un autre avantage, bien qu'il puisse paraître anodin, est l'ordre dans lequel on va lire un du code Go :
```go
func foo(a int, b int) int {
    // ...
}
```
On peut aisément lire (dans le sens de lecture occidental) *"la fonction `foo` attend un paramètre `a` de type `int`, un paramètre `b` de type `int` et renvoie un `int`"*.

#### 4. La gestion des dépendances
Go embarque un gestionnaire de packages extrêmement puissant permettant d'installer et gérer les dépendances de nos programmes.
La commande `go get` permet de télécharger un package distant.
En rajoutant l'option `-u` (pour "update"), elle met à jour le package en installant la dernière version.
On peut directement importer dans notre code des packages en provenance d'un repo GitHub, GitLab, ou autre :
```go
import (
    // stdlib
    "fmt"
    "strings"
    
    // package distant
    "github.com/path/to/repo"
)
```
Si la syntaxe est de prime abord assez déroutante, force est de reconnaître que le mécanisme est extrêmement puissant.

#### 5. Le tooling
Que serait un langage de programmation sans bons outils ?
Si beaucoup de langages offrent de très bons outils, Go n'est pas en reste et propose, en plus de son gestionnaire de dépendances, [un framework de tests unitaires](https://vpenando.github.io/articles/fr/2020/go_test.html), et même un outil d'analyse de code ([`go vet`](https://golang.org/cmd/vet/)) !
De plus, l'équipe de développement propose un super [plugin pour VS Code](https://code.visualstudio.com/docs/languages/go) !

#### 6. Les goroutines
Nous ne pouvions pas parler des atouts de Go sans aborder les *goroutines*, et, par extension, des *channels*.
Il s'agit de la manière dont Go gère le parallélisme et la concurrence.
Là où en C# (par exemple), une méthode `async` est nécessairement asynchrone, n'importe quelle fonction Go peut appeler *n'importe quelle fonction* de manière asynchrone. Pour ce faire, on préfixe l'appel du mot-clé `go` :
```go
func foo() {
    // ...
}

// ...
go foo()
```
Pour communiquer avec une goroutine, on passe par des *channels*, dans lesquels on peut lire et écrire. Exemple :
```go
func foo(ch chan string) {
    ch <- "Hello, world!"
}
 
// ...

ch := make(chan string)
go foo(ch)
msg := <-ch
fmt.Println(msg)
}
```
([Lien](https://play.golang.org/p/jaUhGBPi3PP)).

---

### Points faibles de Go

- syntaxe ?
- conventions de nommage
- simplicité = limitations (génériques, const correctness, ...)
- gc ?
- le type `interface{}`
- `nil`

---

### Conclusion