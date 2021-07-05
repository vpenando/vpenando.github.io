## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) SOLID 2/5 - L'OCP

---

### Introduction

Cet article est le second d'une série dédiée aux principes SOLID. Pour consulter le premier, c'est [par ici](/articles/fr/2021/solid_srp.html) !

L'OCP (**O**pen / **C**losed **P**rinciple, soit "principe ouvert / fermé" en français) énonce la règle suivante :
> Toute entité (classe, fonction, ...) doit être *ouverte aux extensions*, mais *fermée aux modifications*.

Formulé comme ça, l'OCP est encore moins clair que la définition d'une monade. Pourtant, la bonne pratique derrière cette règle est extrêmement simple :
> Tout code testé et validé ne doit pas être modifié afin de ne pas intégrer de régression.

Mais du coup, comment étendre un code étant à la fois "ouvert" et "fermé" ?

Voyons tout ça avec quelques exemples ! À cet effet, j'emploierai le langage Go ~~parce que je fais ce que je veux~~ afin de varier un peu.

---

### Cas d'école : l'aire d'une forme géométrique

L'exemple le plus classique, très évocateur, est celui du calcul de l'aire d'une forme géométrique sans connaître ladite forme.
Pimentons un peu l'exercice en créant plutôt une fonction faisant la somme des aires d'une liste de formes géométriques.
```go
type Rectangle struct {
    Width  float32
    Height float32
}

type Circle struct {
    Radius float32
}

func sumAreas(values ...interface{}) float32 {
    var sum float32
    for _, value := range values {
        switch shape := value.(type) {
        case Rectangle:
            sum += shape.Width * shape.Height
        case Circle:
            sum += math.Pi * shape.Radius * 2
        // ...
        }
        // Cas d'erreur
        panic("unknown shape :(")
    }
    return sum
}
```
Mettons de côté l'absence d'invariants liés au type `float32`, cet exemple n'étant là qu'à but illustratif.

L'approche de cette fonction est simple : vérifier le type de chaque forme via un `switch`, et appliquer une formule en conséquence.
Par ailleurs, la vérification du type de `value` étant effectuée au *runtime*, le code suivant est parfaitement valide :
```go
rectangle := Rectangle{Width: 10, Height: 20}
circle := Circle{Radius: 20}
impostor := "blue"

fmt.Println(sumAreas(rectangle, circle, impostor)) // Compile, mais...
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

Outre le problème lié à la généricité trop permissive, notre fonction `sumAreas()` a une autre faille : si l'on doit rajouter une forme, il faut la modifier.
Cela induit que l'OCP n'est pas respecté, car `sumAreas()` n'est pas *fermée aux modifications*.
Or, la modifier induit que l'on peut introduire une régression, ce qui est précisément la problématique à laquelle répond l'OCP.

--- 

### Solution

La solution au problème évoqué ci-dessus est évidente : utiliser une couche d'abstraction (ici une interface) plutôt que de vérifier à la main le type de notre variable.

***Note -** D'une manière générale, le [RTTI](https://en.wikipedia.org/wiki/Run-time_type_information) est bien souvent une mauvaise idée et son utilisation reste par défaut à prohiber.*

Pour ce faire, il nous faut d'abord créer l'interface adéquate :
```go
type Shape interface {
    area() float32
}
```
Cette interface apporte deux garanties :
- Les types sous-jacents seront forcément valides ;
- Le calcul de l'aire est déporté hors de la fonction appelante.

À présent, implémentons la méthode `area()` pour nos types `Rectangle` et `Circle` !
```go
func (rectangle Rectangle) area() float32 {
    return rectangle.Width * rectangle.Height
}

func (circle Circle) area() float32 {
    return math.Pi * circle.Radius * 2
}
```
Enfin, notre fonction `sumAreas()` devient quant à elle :
```go
func sumAreas(shapes ...Shape) float32 {
    var sum float32
    for _, shape := range shapes {
        sum += shape.area()
    }
    return sum
}
```
Retentons l'exemple de la partie précédente :
```go
rectangle := Rectangle{Width: 10, Height: 20}
circle := Circle{Radius: 20}
impostor := "blue"

fmt.Println(sumAreas(rectangle, circle, impostor))
```
Sortie :
```
/path/to/file.go:X:Y: cannot use impostor (type string) as type Shape in argument to sumAreas:
	string does not implement Shape (missing area method)
```
Paf ! Notre imposteur ne passe pas le contrôle technique et le code ne compile pas !

Mais surtout, cette option propose deux avantages :
- On ne peut passer qu'un type qui satisfait l'interface `Shape` ;
- Pour étendre `sumAreas()`, plus besoin de la modifier !

Ainsi, notre fonction respecte l'OCP : elle est ouverte aux extensions (par le biais de `Shape`), mais fermée aux modifications car son code ne changera pas !
Une fois celle-ci correctement testée, nous aurons alors la garantie qu'elle fera bien ce que l'on attend d'elle !
