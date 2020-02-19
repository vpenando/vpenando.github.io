## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Les interfaces en Go

---

### Sommaire
* Introduction
* Structures & méthodes
* Implémenter une interface
* Conclusion

---

### Introduction
---
En orienté objet, une interface est une structure déclarant des méthodes (et propriétés, dans le cas du C#) que devront exposer les classes l'implémentant.

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
    
    public void Bar()
    {
        // ...
    }
    
    public void Baz()
    {
        // ...
    }
}
```
Il ne s'agit donc ni plus ni moins que d'un contrat, où toute classe implémentant `IFoo` devra exposer son interface publique, c'est à dire ses méthodes (et propriétés) publiques.
L'interface `IFoo` définit un contrat, que remplit `Foo` en implémentant ses méthodes `Bar` et `Baz`.

La notion d'interface existe également en Go :
```go
type MyInterface interface {
    foo() int32
    bar()
}
```
Ici, l'interface `MyInterface` expose deux méthodes, `foo` et `bar`. Les structures implémentant cette interface doivent donc faire de même.

---

### Structures & méthodes
Soit la structure suivante :
```go
type User struct {
    Name string
}
```
Nous aimerions pouvoir, par exemple, faire ceci :
```go
bob := User{Name: "Bob"}
bob.Greet()  // "Hello, I'm Bob!"
```
Pour ce faire, créons la méthode `Greet` :
```go
import "fmt"

func (self User) Greet() {
    fmt.Printf("Hello, I'm %s!", self.Name)
}
```
Cette syntaxe diffère quelque peu de la manière habituelle de créer une fonction. Elle dispose d'un paramètre supplémentaire, le "receiver", ici nommé `self`. Il est possible de passer un "receiver" par valeur ou par pointeur :
```go
type Foo struct {}

// par valeur
func (self Foo) Bar() {
    // ...
}

// par pointeur
func (self *Foo) Baz() {
    // ...
}
```
La différence entre ces deux modes de fonctionnement est la même qu'en C ou C++. En passant un paramètre par valeur, une copie sera créée. Par pointeur, seule son adresse sera copiée, ce qui est parfois beaucoup plus léger si l'objet occupe un grand espace mémoire. De fait, on ne peut modifier l'instance courante que via le passage par pointeur ; dans le cas d'un passage par valeur, on ne modifiera que sa copie. (À quelques exceptions près, où la structure contient un pointeur)

La syntaxe pour accéder aux membres de `self` ou appeler ses méthodes ne change pas, que l'on utilise le passage par valeur ou par pointeur. Nous n'avons donc pas besoin d'utiliser un opérateur comme `->` comme en C.

---

### Implémenter une interface
En Go, il n'existe pas d'héritage à proprement parler. Ainsi, contrairement à d'autres langages, il n'est pas possible de faire ceci :
```go
type Base struct {}

type Child struct: Base {}  // <- invalide
```
Sans pour autant aller dans le détail, il existe la notion d' "embedding" en Go, qui se rapproche de l'héritage. Plus d'informations sur [cette page](https://travix.io/type-embedding-in-go-ba40dd4264df).

Pour implémenter (nous devrions dire "satisfaire") une interface, c'est donc extrêmement simple : il suffit que la structure expose les méthodes de l'interface !

Exemple :
```go
type IUser interface {
    Greet()  // nous déclarons une méthode 'Greet()'
}

type User struct {
    Name string
}

func (self User) Greet() {
    fmt.Printf("Hello, I'm %s!", self.Name)
}
```
Et voilà ! Notre structure `User` satisfait bel et bien `IUser`.

---

### Conclusion

Personnellement, je ne suis pas particulièrement adepte du fait de pouvoir implicitement implémenter une interface. Par habitude, j'aime, à la lecture d'une classe ou d'une structure, savoir quelles interfaces sont implémentées. Néanmoins, cela fait partie de l'ADN de Go, afin de garder sa simplicité et son expressivité, comme l'énonce la [documentation en ligne](https://golang.org/doc/faq#principles).

Ce mécanisme est néanmoins très puissant, et, combiné avec la possibilité d'implémenter des méthodes pour *n'importe quel type* permet une énorme souplesse. Il devient alors possible de faire en sorte qu'un `int` implémente une interface donnée, par exemple !
