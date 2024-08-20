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
En effet, en dépit d'une syntaxe très épurée et [d'un nombre de mots-clés très faible](https://go.dev/ref/spec#Keywords), il permet de faire à peu près n'importe quoi, d'une simple application CLI à de l'embarqué, en passant entre autres à des services web.

En parlant de services web, il est très courant d'avoir un fichier de configuration *-souvent en JSON-* à côté de notre application.
Je pense notamment au fichier `appsettings.json` que les développeurs C# connait.
Celui-ci peut contenir diverses informations plus ou moins sensibles : URL d'un service distant, identifiants de connexion, et j'en passe.

## <a name="embed-go">Et `embed`, c'est quoi ?</a>

Go ne serait pas si bien adapté au web s'il ne permettait pas *-élégamment-* de lire un ou plusieurs fichiers de configuration.

## <a name="syntaxe">Syntaxe</a>

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

// La fonction init() est automatiquement appelée
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

## <a name="contraintes">Contraintes</a>

## <a name="conclusion">Conclusion</a>
