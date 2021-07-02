## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) SOLID 2/5 - L'OCP

---

### Introduction


---

### Cas d'école : l'aire d'une forme géométrique

L'exemple le plus classique, très évocateur, est celui du calcul de l'aire d'une forme géométrique sans connaître ladite forme.
Pimentons un peu l'exercice en ajoutant une fonction faisant la somme d'une liste de formes géométriques.
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

func sumAreas(values ...interface{}) float32 {
    var sum float32
    for _, value := range values {
        sum += area(value)
    }
    return sum
}
```
Mettons de côté l'absence d'invariants liés au type `float32`, cet exemple n'étant là qu'à but illustratif.

La vérification du type de `value` est effectuée au *runtime*. Ce faisant, le code suivant est parfaitement valide :
```go
rectangle := Rectangle{Width: 10, Height: 20}
circle := Circle{Radius: 20}
impostor := "blue"
	
fmt.Println(area(rectangle))                      // OK
fmt.Println(area(circle))                         // OK
fmt.Println(area(impostor))                       // OK (mais appelle panic())
fmt.Println(sumAreas(rectangle, circle, impostor) // Idem
```
Output :
```
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

Outre le problème lié à la généricité trop permissive, notre fonction `area()` a une autre faille : si l'on doit rajouter une forme, il faut la modifier.
Cela induit que l'OCP n'est pas respecté, car `area()` n'est pas *fermée aux modifications*.

--- 

### Solution

La solution au problème évoqué ci-dessus est évidente : il convient d'utiliser une couche d'abstraction (ici une interface) plutôt que de vérifier à la main le type de notre variable.

***Note -** D'une manière générale, le [RTTI](https://en.wikipedia.org/wiki/Run-time_type_information) est bien souvent une mauvaise idée et son utilisation reste par défaut à prohiber.*

Pour ce faire, il nous faut d'abord créer l'interface adéquate :
```go
type Shape interface {
    area() float32
}
```
Ensuite, implémentons la méthode `area()` pour nos types `Rectangle` et `Circle` !
```go
func (rectangle Rectangle) area() float32 {
    return rectangle.Width * rectangle.Height
}

func (circle Circle) area() float32 {
    return math.Pi * circle.Radius * 2
}
```
Enfin, s'il ne fait plus sens de garder notre fonction `area()` (devenue méthode membre de `Shape`), `sumAreas()` devient quant à elle :
```go
func sumAreas(shapes ...Shape) float32 {
    var sum float32
    for _, shape := range shapes {
        sum += shape.area()
    }
    return sum
}
```
Cette option propose deux avantages :
- On ne peut passer qu'un type qui satisfait l'interface `Shape` ;
- Pour étendre `sumAreas()`, plus besoin de la modifier !

Ainsi, notre fonction respecte l'OCP : elle est ouverte aux extensions (par le biais de `Shape`), mais fermée aux modifications car son code ne changera pas !
