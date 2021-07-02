## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) SOLID 2/5 - L'OCP

---

### Introduction


---

### Cas d'école : l'aire d'une forme géométrique

Le cas le plus classique, très évocateur, est celui du calcul de l'aire d'une forme géométrique sans connaître ladite forme :
```go
type Rectangle struct {
    Width  float32
    Height float32
}

type Circle struct {
    Radius float32
}

func area(value interface{}) float32 {
    // Nous vérifions le type de notre forme
    switch shape := value.(type) {
    case Rectangle:
        return shape.Width * shape.Height
    case Circle:
        return math.Pi * shape.Radius * 2
    // ...
    }
    // Cas d'erreur
    panic("unknown shape :(")
}
```
La vérification du type de `value` est effectuée au *runtime*. Ce faisant, le code suivant est parfaitement valide :
```go
rectangle := Rectangle{Width:10, Height:20}
circle := Circle{Radius:20}
	
fmt.Println(area(rectangle))  // OK
fmt.Println(area(circle))     // OK
fmt.Println(area("impostor")) // KO
```
***Note -** D'une manière générale, le [RTTI](https://en.wikipedia.org/wiki/Run-time_type_information) est bien souvent une mauvaise idée et son utilisation reste par défaut à prohiber.*

```go
type Shape interface {
    area() uint
}
```

