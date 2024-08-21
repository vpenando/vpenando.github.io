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

Go est un langage que j'apprécie beaucoup, à la fois pour sa simplicité, mais également pour les vastes possibilités qu'il offre.
En effet, malgré une syntaxe très épurée et [un nombre de mots-clés très faible](https://go.dev/ref/spec#Keywords), il permet de faire à peu près n'importe quoi, d'une simple application CLI à de l'embarqué, en passant entre autres à des services web.

En parlant de services web, il est très courant d'avoir un fichier de configuration *-souvent en JSON-* à côté de notre application.
Je pense notamment au fichier `appsettings.json` que les développeurs C# connaissent.
Celui-ci peut contenir diverses informations plus ou moins sensibles : URL d'un service distant, identifiants de connexion, et j'en passe.

## <a name="embed-go">Et `embed`, c'est quoi ?</a>

Go ne serait pas si bien adapté au web s'il ne permettait pas *-élégamment-* de répondre à la problématique évoquée dans la section précédente, à savoir lire des informations depuis un fichier de configuration.

En Go, `embed` est un *package* permettant d'inclure le contenu d'un ou plusieurs fichiers directement dans le binaire compilé.
Ce faisant, ce ou ces fichiers peuvent être lus dès le démarrage du programme.
Mieux encore, une fois notre application compilée (et livrée !), il n'y a aucune trace sur le disque d'un quelconque fichier contenant des informations possiblement critiques.

Tout est directement *embarqué* dans l'application.

## <a name="syntaxe">Syntaxe</a>

***Note -** Pour lire la suite de cet article, je considère que vous connaissez les rudiments du langage.*

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

Ce genre de commentaires est assez courant en Go ; peut-être avez-vous déjà rencontré `//go:build` ou encore `//go:noinline`.
Je n'entrerai cependant pas dans les détails concernant ce genre de spécificités.

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

Pour cela, la syntaxe est quasiment la même que l'exemple précédent :
```go
package main

import (
    "embed"
    "encoding/json"
    "fmt"
)

var (
    //go:embed config.debug.json config.release.json
    configFiles embed.FS

    conf config
)

type config struct {
    ApiKey    string `json:"api_key"`
    ServerUrl string `json:"server_url"`
}

const (
    // À commenter ou décommenter selon les besoins
    environment = "debug"
    //environment = "release"

    configFileName = "config." + environment + ".json"
)

func init() {
    file, err := configFiles.ReadFile(configFileName)
    if err != nil {
        panic(fmt.Sprintf("failed to read config: %s", err))
    }
    if err := json.Unmarshal(file, &conf); err != nil {
        panic(fmt.Sprintf("failed to read config: %s", err))
    }
}

func main() {
    fmt.Println("conf.ServerUrl =", conf.ServerUrl)
    fmt.Println("conf.ApiKey =", conf.ApiKey)
}
```

Dans cet exemple, nous ajoutons le contenus des fichiers `config.debug.json` et `config.release.json` dans notre application grâce au type `embed.FS`.
Puis, selon la valeur de `environment`, nous pouvons lire l'un ou l'autre grâce à la méthode `ReadFile` de la variable `configFiles`.

Attention cependant : si au moins un fichier n'existe pas, alors le code entier ne compilera pas !
Il vous faut donc créer tous les fichiers spécifiés.

## <a name="conclusion">Conclusion</a>

En somme, le *package* `embed` vous permet d'inclure dans le binaire compilé un ou plusieurs fichiers présents sur le disque.
Cela permet de ne pas avoir un fichier de config en clair sur la machine où s'exécute votre application.

De plus, c'est une optimisation possible plutôt intéressante qui permet d'éviter d'ouvrir, lire puis refermer un fichier au runtime (je pense notamment à [`html/template`](https://pkg.go.dev/html/template) où il nous faudra simplement appeler la fonction [`Parse`](https://pkg.go.dev/html/template#Template.Parse)), tout en ayant l'assurance que la ressource demandée existera puisque compilée avec le binaire.

Par ailleurs, nous avons abordé les trois façons possibles de stocker le contenu d'un ou plusieurs fichiers :
- Dans une variable de type `string`, qui suffit pour une grande partie des cas ;
- Dans une variable de type `[]byte`, particulièrement intéressant pour les fichiers JSON ;
- Dans une variable de type `embed.FS`, qui permet de lire plusieurs fichiers d'un seul coup.

Enfin, il est à noter que si au moins un fichier manque à l'appel, votre application ne compilera pas.
