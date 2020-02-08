## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Les interfaces en Go

---

### Sommaire

---

```go
type MyInterface interface {
    foo() int32
    bar()
}
```

---

### Structures & méthodes
Soit la structure suivante :
```go
type Foo struct {
    Name string
}
```
Nous aimerions pouvoir, par exemple, faire ceci :
```go
foo := Foo{Name: "Bob"}
foo.Greet()  // "Hello, I'm Bob!"
```
Pour ce faire, créons la méthode `Greet` :
```go
import "fmt"

func (self *Foo) Greet() {
    fmt.Printf("Hello, I'm %s!", "Bob")
}
```
