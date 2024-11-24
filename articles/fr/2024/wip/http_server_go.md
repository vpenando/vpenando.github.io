#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Créer un serveur HTTP en Go à partir de zéro

---

### Sommaire
* [Introduction](#introduction)
* [Création du projet et installation des dépendances](#create-project)
* [Structure (basique) de notre project](#project-architecture)
* [Premier exemple : le fameux "Hello, world!"](#first-implementation)
* [Un saut dans le grand bain](#second-implementation)

---

## <a name="introduction">Introduction</a>

Go est à mon sens un excellent langage, notamment pour les performances qu'il offre, mais aussi pour sa syntaxe épurée permettant de se concentrer sur l'essentiel.
Bien qu'initialement présenté comme langage système, il est aujourd'hui largement utilisé pour écrire des services web.

Et c'est précisément dans ce contexte que j'écris cet article, au cours duquel je vais détailler pas à pas la création d'un serveur HTTP en Go, en utilisant [Gin](https://github.com/gin-gonic/gin).

---

## <a name="create-project">Création du projet et installation des dépendances</a>

Pour la suite de cet article, je considère que vous avez déjà Go installé sur votre poste.

Afin de créer un nouveau projet, il suffit d'initialiser un nouveau module via la commande `go mod init <NOM_DU_PROJET>`.
Ainsi donc, vous pouvez créer un projet (appelons-le `http_server`) en créant un dossier dédié et en y exécutant la commande `go mod init http_server`.

***Note -** Il est souvent d'usage de créer un repo pour un projet, et de l'ajouter dans le nom de ce dernier (exemple : `go mod init github.com/vpenando/http_server`). Dans le cas d'un projet de démo, je suis allé au plus simple.*

À présent, il vous faut installer les dépendances dont vous aurez besoin.
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
* Le dossier `pkg`, par opposition au dossier `internal`, contient tout le code destiné à être exposé en dehors du projet ; typiquement, les DTOs renvoyés par notre serveur HTTP.

Quant au contenu de ces dossiers, nous nous y intéresserons dans un second temps.

---

## <a name="first-implementation">Premier exemple : le fameux "Hello, world!"</a>

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
À présent, si vous exécutez la commande `go run cmd/http_server/main.go` et que vous vous rendez sur `http://localhost:8080/`, vous verrez un (magnifique) texte s'afficher à l'écran.

Et voilà, vous savez faire un "Hello, world!" avec Gin !
Bon, c'est bien beau, mais cela ne nous avance cependant pas encore à grand chose.

---

## <a name="second-implementation">Un saut dans le grand bain</a>

Je vous propose une implémentation un peu plus conséquente, et surtout un peu plus réaliste.
Pour l'exemple, imaginons que nous voulons créer une web API en charge de renvoyer un ou plusieurs utilisateurs depuis, par exemple, une base de données.

Nous aurions alors les cas d'usage suivants :
* Lister tous les utilisateurs
* Rechercher un utilisateur par nom et/ou prénom
* Récupérer un utilisateur à partir de son ID

Attelons-nous à créer une structure adaptée !

***Note -** Je vais volontairement adopter une architecture très simple ; pas d'Hexagonal ou de Clean Architecture, qui auront chacune droit à un ou plusieurs articles dédiés.*

Pour commencer, créons un sous-dossier `entities/user/` dans `internal`, et ajoutons-y un fichier `user.go` :
```go
package user

import (
    "github.com/google/uuid"
)

// Convention Go : "user" est déjà le nom du package,
// donc inutile de le répéter dans le nom du type !
type ID uuid.UUID

type User struct {
    id        ID
    firstName string
    lastName  string
}

func New(id ID, firstName string, lastName string) User {
    u := User{
        id:        id,
        firstName: firstName,
        lastName:  lastName,
    }
    return u
}

func (u User) ID() ID {
    return u.id
}

func (u User) FirstName() string {
    return u.firstName
}

func (u User) LastName() string {
    return u.lastName
}
```
Voilà pour la partie utilisateur : un ID, un prénom, et un nom. Simple, basique. Vous avez les bases.

À présent, créons le repository associé ; toujours dans le dossier `user`, créez le fichier `user_repository` et collez-y le code suivant :
```go
package user

// Convention Go : "user" est déjà le nom du package,
// donc inutile de le répéter dans le nom du repo !
type Repository interface {
    ListUsers(skip, limit uint) ([]User, error)
    SearchUser(input string) ([]User, error)
    GetUserByID(userID ID) (User, error)
}
```
Vous reconnaissez les trois *usecases* évoqués plus haut, n'est-ce pas ?
Pour implémenter cette interface (ou devrais-je dire *satisfaire* cette interface), je vous propose de créer un dossier `repositories/` dans `internal/`.

À ce stade, notre projet doit avoir la structure suivante :
```
http_server/
    cmd/http_server/
        - main.go
    internal/
        entities/
            - user.go
            - user_repository.go
        repositories/
    pkg/
```
Créons l'implémentation de notre `user.Repository` : pour ce faire, créons un fichier `user_repository.go` dans `internal/repositories`.
Pour rester le plus simple possible et ne pas avoir à gérer une base de données (le but de cet article est de créer un serveur HTTP, je le rappelle), notre repo travaillera avec une collection statique.

En voici l'implémentation complète :
```go
package repositories

import (
	"errors"
	"strings"

	"github.com/google/uuid"
	"github.com/vpenando/http_server/internal/entities/user"
)

// Le type concret n'est pas exposé
type userRepository struct {
}

func NewUserRepository() user.Repository {
	repo := userRepository{}
	return repo
}

func (r userRepository) ListUsers(skip, limit uint) ([]user.User, error) {
	skip = min(skip, uint(len(allUsers)))
	limit = min(limit, uint(len(allUsers)))
	users := allUsers[skip:limit]
	return users, nil
}

func (r userRepository) SearchUser(input string) ([]user.User, error) {
	users := make([]user.User, 0, len(allUsers))
	for _, u := range allUsers {
		if containsIgnoreCase(u.FirstName(), input) || containsIgnoreCase(u.LastName(), input) {
			users = append(users, u)
		}
	}
	return users, nil
}

func (r userRepository) GetUserByID(userID user.ID) (user.User, error) {
	for _, u := range allUsers {
		if u.ID() == userID {
			return user.User{}, nil
		}
	}
	return user.User{}, errors.New("user not found")
}

var allUsers = []user.User{
	user.New(parseUserID("161966ed-9111-4956-af80-b4bb6bdb5466"), "John", "Doe"),
	user.New(parseUserID("7b551512-138c-49c3-a7f9-a532c4d2dafe"), "John", "Doe"),
}

func parseUserID(id string) user.ID {
	return user.ID(uuid.MustParse(id))
}

func containsIgnoreCase(s, substr string) bool {
	return strings.Contains(
		strings.ToLower(s),
		strings.ToLower(substr),
	)
}

```


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
