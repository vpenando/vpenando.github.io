#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Go : La directive `embed`

---

### Sommaire
* [Introduction](#introduction)
* [Et `embed`, ça sert à quoi ?](#embed-go)
* [Syntaxe](#syntaxe)
* [Contraintes](#contraintes)
* [Conclusion](#conclusion)

---

## <a name="introduction">Introduction</a>

Go est un langage que j'apprécie beaucoup, à la fois pour sa simplicité, mais également pour les possibilités qu'ils offre en dépit de celle-ci.
En effet, malgré une syntaxe très épurée et [d'un nombre de mots-clés très faible](https://go.dev/ref/spec#Keywords), il permet de faire à peu près n'importe quoi, d'une simple application CLI à de l'embarqué, en passant entre autres à des services web.

En parlant de services web, il est très courant d'avoir un fichier de configuration *-souvent en JSON-* à côté de notre application.
Je pense notamment au fichier `appsettings.json` que les développeurs C# connaissent.
Celui-ci peut contenir diverses informations plus ou moins sensibles : URL d'un service distant, identifiants de connexion, et j'en passe.

## <a name="embed-go">Et `embed`, c'est quoi ?</a>

Go ne serait pas si bien adapté au web s'il ne permettait pas *-élégamment-* de répondre à la problématique évoquée dans la section précédente.

En Go, `embed` est une directive permettant d'inclure le contenu d'un ou plusieurs fichiers directement dans le binaire compilé.
Ce faisant, ce ou ces fichiers peuvent être lus dès le démarrage du programme.
Mieux encore, une fois notre application compilée, il n'y a aucune trace sur le disque d'un quelconque fichier contenant des informations possiblement critiques.

Tout est directement *embarqué* dans une chaîne de caractère ou via le type dédié, `embed.FS`.

## <a name="syntaxe">Syntaxe</a>

**Note -** Pour lire la suite de cet article, que considère que vous connaissez les rudiments du langage.

La syntaxe pour *embarquer* le contenu d'un fichier texte est un peu déroutante, aussi je vous mets à disposition un exemple de code complet que nous allons analyser ensemble.

```go
package main

import (
    _ "embed"
    "encoding/json"
    "fmt"
)

//go:embed config.json
var configJson string

var config Config

// La fonction init() est automatiquement appelée, avant main()
func init() {
    if err := json.Unmarshal([]byte(configJson), &config); err != nil {
        panic(fmt.Sprintf("failed to read config: %s", err))
    }
}

type Config struct {
    ServerUrl string `json:"server_url"`
}

func main() {
    fmt.Println("config.ServerUrl =", config.ServerUrl)
}
```

Tout d'abord, il nous faut inclure le package `embed`. Comme nous ne l'utilisons pas directement, il sera préfixé d'un `_` pour éviter toute erreur de compilation.
En effet, Go ne nous laisse pas importer un package que nous n'utilisons pas, à moins de l'importer sous la forme `_ "package_name"`.
Je n'entrerai pas dans les détails sur ce point, car cela n'est pas l'objet de cet article.

## <a name="contraintes">Contraintes</a>

## <a name="conclusion">Conclusion</a>
