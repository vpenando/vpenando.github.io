#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Créer un serveur HTTP en Go à partir de zéro

---

### Sommaire
* [Introduction](#introduction)
* [Création du projet et installation des dépendances](#create-project)
* [Structure (basique) de notre project](#project-architecture)
* [Première implémentation : le fameux "Hello, world!"](#first-implementation)

---

## <a name="introduction">Introduction</a>

Go est à mon sens un excellent langage, notamment pour les performances qu'il offre, ainsi que sa syntaxe épurée, permettant de se concentrer sur l'essentiel avant tout.
Bien qu'initialement présenté comme langage système, il est aujourd'hui largement utilisé pour écrire des services web.

Au cours de cet article, je vais détailler pas à pas la création d'un serveur HTTP en Go, en utilisant [Gin](https://github.com/gin-gonic/gin).

---

## <a name="create-project">Création du projet et installation des dépendances</a>

Pour la suite de cet article, je considère que vous avez déjà l'utilitaire Go installé sur votre poste.

Afin de créer un nouveau projet, il suffit d'initialiser un nouveau module via la commande `go mod init <NOM_DU_PROJET>`.
Ainsi donc, vous pouvez créer un projet (appelons-le `http_server`) en créant un dossier dédié et en y exécutant la commande `go mod init http_server`.

***Note -** Il est souvent d'usage de créer un repo pour un projet, et de l'ajouter dans le nom de ce dernier (exemple : `go mod init github.com/vpenando/http_server`). Dans le cas d'un projet de démo, je suis allé au plus simple.*

À présent, il vous faut à présent installer les dépendances donc vous aurez besoin.
Pour cela, rien de plus simple : il suffit de taper la commande `go get <PACKAGES>` (plus d'informations [ici](https://pkg.go.dev/cmd/go#hdr-Add_dependencies_to_current_module_and_install_them)).

Dans notre cas, nous allons pour le moment installer Gin et [`uuid`](https://github.com/google/uuid) :
```
go get -u github.com/gin-gonic/gin github.com/google/uuid
```

***Note -** Le flag `-u` (pour "update") permet de mettre à jour le package s'il est déjà installé.*

---

## <a name="project-architecture">Structure (basique) de notre project</a>

Afin d'être "Go-compliant", nous allons utiliser une structure conventionnelle.
Pour davantage d'informations, je vous renvoie à [cet article](https://github.com/golang-standards/project-layout/blob/master/README_fr.md#les-r%C3%A9pertoires-go).

Dans notre cas, le projet sera structuré comme suit :
```
http_server/
    cmd/http_server/
        - main.go
    internal/
         - ...
    pkg/
        - ...
```
* Le dossier `cmd` contient différents dossiers, chacun correspondant à une application. Dans notre cas, il ne contiendra que `http_server`, lui-même contenant le point d'entrée du programme, `main.go`.
* Le dossier `internal` contient tout le code utilisé par notre programme, mais qui ne doit pas être exposé en dehors de celui-ci. J'ai d'ailleurs déjà [rédigé un article sur ce sujet](https://vpenando.github.io/articles/fr/2024/go_internal.html).
* Le dossier `pkg`, par opposition au dossier `internal`, contient tout le code destiné à être exposé en dehors du projet ; typiquement, les DTOs renvoyés par notre serveur.

Quant au contenu de ces dossiers, nous nous y intéresserons dans un second temps.

---

## <a name="first-implementation">Première implémentation : le fameux "Hello, world!"</a>

Ouvrez le fichier `cmd/http_server/main.go` et collez-y le code suivant :
```go
package main

import (
	"net/http"

	"github.com/gin-gonic/gin"
)

func main() {
	r := gin.Default()
	r.GET("/", func(c *gin.Context) {
		c.String(http.StatusOK, "Hello, world!")
	})
	r.Run()
}
```
À présent, si vous exécutez la commande `go run cmd/http_server/main.go` et que vous vous rendez sur `http://localhost:8080/`, vous verrez un magnifique texte s'afficher à l'écran !



```
http_server/
    cmd/http_server/
        - main.go
    internal/
        endpoints/
            - ...
        usecases/
        services/
            - ...
    pkg/
        dtos/
            - ...  
```



```
go get -u github.com/stretchr/testify/assert
```
