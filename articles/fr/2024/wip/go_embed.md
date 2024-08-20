#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Go : La directive `embed`

---

### Sommaire
* [Introduction](#introduction)
* [Les directives en Go](#directives-go)
* [Et `embed`, ça sert à quoi ?](#embed-go)
* [Syntaxe](#syntaxe)
* [Contraintes](#contraintes)
* [Conclusion](#conclusion)

---

## <a name="introduction">Introduction</a>

## <a name="directives-go">Les directives en Go</a>

## <a name="embed-go">Et `embed`, ça sert à quoi ?</a>

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
