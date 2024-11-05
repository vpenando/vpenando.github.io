#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Créer un serveur HTTP en Go à partir de zéro

---

### Sommaire
* [Introduction](#introduction)

---

## <a name="introduction">Introduction</a>

Go est à mon sens un excellent langage, notamment pour les performances qu'il offre, ainsi que sa syntaxe épurée, permettant de se concentrer sur l'essentiel avant tout.

Au cours de cet article, je vais détailler pas à pas la création d'un serveur HTTP en Go.

`go mod init github.com/vpenando/http_server`

#### Installation des dépendances

- `go get -u github.com/gin-gonic/gin`
- `go get -u github.com/google/uuid`

***Note -** Le flag `-u` (pour "update") permet de mettre à jour le package s'il est déjà installé.*
