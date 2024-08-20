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

var (
    //go:embed config.json
    configJson string

    conf config
)

type config struct {
    ApiKey    string `json:"api_key"`
    ServerUrl string `json:"server_url"`
}

// La fonction init() est automatiquement appelée, avant main()
func init() {
    if err := json.Unmarshal([]byte(configJson), &conf); err != nil {
        panic(fmt.Sprintf("failed to read config: %s", err))
    }
}

func main() {
    fmt.Println("conf.ServerUrl =", conf.ServerUrl)
    fmt.Println("conf.ApiKey =", conf.ApiKey)
}
```

Tout d'abord, il nous faut inclure le package `embed`. Comme nous ne l'utilisons pas directement, il sera préfixé d'un `_` pour éviter toute erreur de compilation.
Comme le mentionne [la doc officielle](https://go.dev/ref/spec#Import_declarations) :
> To import a package solely for its side-effects (initialization), use the blank identifier as explicit package name [..]

Ensuite, nous créons la variable destinée à stocker le texte contenu dans le fichier, ici `configJson`.
Vous aurez sans aucun doute remarqué la présence de cet étrange commentaire précédant la déclaration de notre variable, n'est-ce pas ?
Sachez que c'est grâce à celui-ci que la magie opère !
En effet, il va indiquer au compilateur de chercher un fichier nommé `config.json` dans le dossier courant, et de stocker son contenu dans la variable déclarée juste après !

Le reste est assez trivial : nous lisons le contenu du fichier au démarrage du programme via la fonction `init()` et affichons les informations lues dans la console.

Notez néanmoins la présence d'un *cast* dans l'appel à `json.Unmarshal`, car cette fonction attend un `[]byte`.
Cela est dû à une simplification de ma part, qui ai déclaré `configJson` comme étant de type `string`, mais sachez que l'on aurait aussi bien pu la déclarer comme `[]byte` :
```go
var (
    //go:embed config.json
    configJson []byte

    conf config
)

// ...

func init() {
    if err := json.Unmarshal(configJson, &conf); err != nil {
        panic(fmt.Sprintf("failed to read config: %s", err))
    }
}
```
Dans le cas d'un fichier JSON, c'est même plus pertinent car cela nous évite d'avoir à explicitement faire une conversion.

Enfin, il est également possible de lire plusieurs fichiers d'un coup grâce au type `embed.FS` !
Imaginez que vous avez plusieurs fichiers de configuration, un pour le mode "debug" et un pour le mode "release", et vous souhaitez pouvoir lire l'un ou l'autre selon l'environnement d'exécution de votre programme.

## <a name="contraintes">Contraintes</a>

## <a name="conclusion">Conclusion</a>
