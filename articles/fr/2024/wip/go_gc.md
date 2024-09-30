#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Go : Optimiser la gestion de la mémoire (et du GC !)

---

### Sommaire
* [Introduction](#introduction)

---

## <a name="introduction">Introduction</a>
- Allocations (stack / heap)
- Notion de GC

---

## <a name="allocations">Optimiser ses allocations</a>

```go
slice := make([]string, 0, 100) // une seule allocation !
```


```go
type I interface {
    doWork()
}

type S struct {}

func (s S) doWork() {
    fmt.Println("working...")
}

func callDoWorkInterface(x I) {
    x.doWork()
}

func callDoWorkConstraint[X I](x X) {
    x.doWork()
}
```

---

## <a name="gc">Comment optimiser les appels au GC ?</a>

```go

```
