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
Mettons de côté l'absence d'invariants liés au type `float32`, cet exemple n'étant là qu'à but illustratif.

La vérification du type de `value` est effectuée au *runtime*. Ce faisant, le code suivant est parfaitement valide :
```go
rectangle := Rectangle{Width: 10, Height: 20}
circle := Circle{Radius: 20}
	
fmt.Println(area(rectangle))  // OK
fmt.Println(area(circle))     // OK
fmt.Println(area("impostor")) // KO
```
Output :
```shell
200
125.66371
panic: unknown shape :(

goroutine 1 [running]:
main.area(...)
	/path/to/file.go:X
main.main()
	/path/to/file.go:Y +0x13a

Program exited: status 2.
```
Comme prévu, notre imposteur s'est fait démasquer et l'appel à `panic()` a bien eu lieu.

***Note -** D'une manière générale, le [RTTI](https://en.wikipedia.org/wiki/Run-time_type_information) est bien souvent une mauvaise idée et son utilisation reste par défaut à prohiber.*

```go
type Shape interface {
    area() uint
}
```

