#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Go : Un point sur le dossier "internal"

---

### Sommaire
* [Introduction](#introduction)
* [D'accord, mais comment on fait en Go ?](#visibility-go)
* ["internal", kézako ?](#internal)

---

## <a name="introduction">Introduction</a>

Lorsque l'on développe un module Go, il est évident que l'on souhaite exposer certains types et certaines fonctions ; autrement, notre module n'a pas grand intérêt, vous en conviendrez.

Toutefois, certains éléments doivent rester internes, tout en étant accessibles au code de notre module.

Typiquement, en C#, il existe mot-clé `internal`, qui rend un type / méthode / property / field (rayer les mentions inutiles) uniquement visible depuis l'assembly courante.
Ainsi, les détails d'implémentation restent bien au chaud, inaccessibles en dehors de leur assembly.

---

## <a name="visibility-go">D'accord, mais comment on fait en Go ?</a>

En C#, nous avons des tas mots-clés concernant la visibilité : `public`, `private`, `protected`, `internal`, et depuis peu `file`.

Néanmoins, en Go, il n'existe que deux types de visibilité : public (toute fonction / type / variable dont le nom commence par une majuscule est visible en dehors du dossier courant), et privé (dont le nom commence par une minuscule) :

Exemple :
```go
package example

// Le type Worker est public
type Worker interface {

    // La fonction DoWork() est publique
    DoWork()
}

// Le type workerImplementation est privé
// Il satisfait toutefois l'interface Worker
type workerImplementation struct {
}

func (wi workerImplementation) DoWork() {
    fmt.Println("doing some stuff...")
}

// La fonction NewWorker est publique, et ce
// bien qu'elle renvoie une instance de workerImplementation.
func NewWorker() Worker {
    wi := workerImplementation{}
    return wi
}
```
Dans le code ci-dessus, nous avons un exemple de code où l'on veut rendre une interface publique mais son implémentation privée.

Mais alors, comment faire cela à l'échelle d'un module, où l'implémentation est dans un dossier distinct ?

---

## <a name="internal">"internal", kézako ?</a>

Il est possible de reproduire en Go un comportement similaire à celui proposé par le mot-clé `internal` en C#.

Pour ce faire, il suffit de créer un dossier, nommé... `internal`.
Tout code présent dans ce dossier ou l'un de ses sous-dossiers est visible uniquement dans le module courant.

J'ai créé un repo GitHub vous permettant de tester ce comportement, [accessible à cette URL](https://github.com/vpenando/visibility).

Si vous voulez essayer, créez un projet Go et exécutez la commande ci-après :
```
go get -u github.com/vpenando/visibility
```

Créez un fichier `main.go` et collez-y le code suivant :
```go
package main

import (
    "github.com/vpenando/visibility"
)

func main() {
    w := visibility.NewWorker()
    w.DoWork()
}
```
Vous vous en doutez, ce code compilera sans soucis, et affichera même le texte `doing some stuff...`.
Rien d'exceptionnel jusque là.

Si nous jetons un oeil au fichier `worker.go`, nous constatons qu'il expose une interface `Worker` et une fonction `NewWorker`.
Mais surtout, il fait référence à `github.com/vpenando/visibility/internal/worker` !

Essayons donc de remplacer le contenu de `main.go` par le code suivant :
```go
package main

import (
    "github.com/vpenando/visibility/internal/worker"
)

func main() {
    w := worker.WorkerImplementation{}
    w.DoWork()
}
```
Dès la compilation, nous aurons alors l'erreur suivante :
```
main.go:4:2: use of internal package github.com/vpenando/visibility/internal/worker not allowed
```
C'est exactement le comportement attendu : le package interne `github.com/vpenando/visibility/internal/worker` est visible depuis les autres fichiers !
Plus exactement, pour citer [le papier de 2014 sur le sujet](https://docs.google.com/document/d/1e8kOo3r51b2BWtTs_1uADIA5djfXhPT36s6eHVRIvaU/edit) :
```
An import of a path containing the element “internal” is disallowed if the importing code is outside the tree rooted at the parent of the “internal” directory.
```
