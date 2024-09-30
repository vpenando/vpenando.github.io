#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Go : Un point sur le dossier "internal"

---

### Sommaire
* [Introduction](#introduction)
* [`internal`, kézako ?](#internal)

---

## <a name="introduction">Introduction</a>

Lorsque l'on développe un package Go, il est évident que l'on souhaite exposer certains types et certaines fonctions ; autrement, notre package n'a pas grand intérêt, vous en conviendrez.

Toutefois, certains éléments doivent rester internes, tout en étant accessibles à notre code.
Typiquement, en C#, il existe mot-clé `internal`, qui rend un type / méthode / property / field (rayer les mentions inutiles) uniquement visible depuis l'assembly courante.
Ainsi, les détails d'implémentation restent bien au chaud, inaccessibles en dehors de leur assembly.

---

## D'accord, mais quel rapport avec Go ?

En C#, nous avons des tas mots-clés concernant la visibilité : `public`, `private`, `protected`, `internal`, et depuis peu `file`.

Néanmoins, en Go, il n'existe que deux types de visibilité : exporté (toute fonction / type / variable dont le nom commence par une majuscule est visible en dehors du dossier courant), et privé (dont le nom commence par une minuscule) :

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

Et c'est là qu'intervient un dossier particulier, le fameux dossier `internal`.

---

## <a name="internal">`internal`, kézako ?</a>

