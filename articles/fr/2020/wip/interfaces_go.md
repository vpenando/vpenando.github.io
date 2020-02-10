## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Les interfaces en Go

---

### Sommaire

---
En orienté objet, une interface est une structure déclarant des méthodes que devront exposer les classes l'implémentant. Il s'agit donc ni plus ni moins qu'un contrat.

Exemple en C# :
```cs
interface IFoo
{
    void Bar();
    void Baz();
}

// et à l'usage :
class Foo: IFoo
{
    // 'Foo' doit implémenter 'IFoo'
    
    void Bar()
    {
        // ...
    }
    
    void Baz()
    {
        // ...
    }
}
```
L'interface `IFoo` définit un contrat, que remplit `Foo` en implémentant ses méthodes `Bar` et `Baz`. La notion d'interface existe également en Go :
```go
type MyInterface interface {
    foo() int32
    bar()
}
```
Ici, l'interface `MyInterface` expose deux méthodes, `foo` et `bar`. Les structures implémentant cette interface doivent donc exposer ces méthodes.

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

func (self Foo) Greet() {
    fmt.Printf("Hello, I'm %s!", self.Name)
}
```
Cette syntaxe diffère quelque peu de la manière habituelle de créer une fonction. Elle dispose d'un paramètre supplémentaire, le "receiver", ici nommé `self`. Il est possible de passer un "receiver" par valeur ou par pointeur :
```go
struct Foo {}

// par valeur
func (self Foo) Bar() {
    // ...
}

// par pointeur
func (self *Foo) Baz() {
    // ...
}
```
La différence entre ces deux modes de fonctionnement est la même qu'en C ou C++. En passant un paramètre par valeur, une copie sera créée. Par pointeur, seule son adresse sera copiée, ce qui est parfois beaucoup plus léger. De fait, on ne peut modifier l'instance courante que via le passage par pointeur.

La syntaxe pour accéder aux membres de `self` ou appeler ses méthodes ne change pas, que l'on utilise le passage par valeur ou par pointeur.

---

### Implémenter une interface
En Go, il n'existe pas d'héritage à proprement parler. Ainsi, contrairement à d'autres langages, il n'est pas possible de faire ceci :
```go
struct Base {}

struct Child: Base {}  // <- invalide
```
Sans pour autant aller dans le détail, il existe la notion d' "embedding" en Go, qui se rapproche de l'héritage. Plus d'informations [sur cette page](https://travix.io/type-embedding-in-go-ba40dd4264df).
