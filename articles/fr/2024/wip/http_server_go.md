#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Créer un serveur HTTP en Go à partir de zéro

---

### Sommaire
* [Introduction](#introduction)
* [Création du projet et installation des dépendances](#create-project)
* [Structure de notre project](#project-architecture)

---

## <a name="introduction">Introduction</a>

Go est à mon sens un excellent langage, notamment pour les performances qu'il offre, ainsi que sa syntaxe épurée, permettant de se concentrer sur l'essentiel avant tout.
Bien qu'initialement présenté comme langage système, il est aujourd'hui largement utilisé pour écrire des services web.

Au cours de cet article, je vais détailler pas à pas la création d'un serveur HTTP en Go, en utilisant [Gin](https://github.com/gin-gonic/gin).

`go mod init github.com/vpenando/http_server`

---

## <a name="create-project">Création du projet et installation des dépendances</a>

Pour la suite de cet article, je considère que vous avez déjà Go installé sur votre poste.

Afin de créer un nouveau projet, il suffit d'initialiser un nouveau module via la commande `go mod init <NOM_DU_PROJET>`.
Ainsi donc, vous pouvez créer un projet (appelons-le `http_server`) en créant un dossier dédié et en y exécutant la commande `go mod init http_server`.

***Note -** Il est souvent d'usage de créer un repo pour un projet, et de l'ajouter dans le nom de ce dernier (exemple : `go mod init github.com/vpenando/http_server`). Dans le cas d'un projet de démo, je suis allé au plus simple.*

À présent, il vous faut à présent installer les dépendances donc vous aurez besoin.
Pour cela, rien de plus simple : il suffit de taper la commande `go get <PACKAGES>` (plus d'informations [ici](https://pkg.go.dev/cmd/go#hdr-Add_dependencies_to_current_module_and_install_them)).

Dans notre cas, nous allons pour le moment installer Gin (mentionné plus haut), et [`uuid`](https://github.com/google/uuid) :
```
go get -u github.com/gin-gonic/gin github.com/google/uuid
```

***Note -** Le flag `-u` (pour "update") permet de mettre à jour le package s'il est déjà installé.*

---

## <a name="project-architecture">Structure de notre project</a>

```
http_server/
  cmd/
    http_server/
      - main.go
  internal/
    routing/
      - ...
    services/
      - ...
  pkg/
    api/
      - ...  
```

```
go get -u github.com/stretchr/testify/assert
```
