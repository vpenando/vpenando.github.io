## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) La gestion des flags en Go

---

### Sommaire
* [Introduction](#introduction)

---

### Introduction
Les habitués de Linux connaissent sans doute la notion de *flag*. Il s'agit d'un moyen de passer des arguments à une application, notamment depuis un terminal.

Exemple :
```sh
ls -l
```
La commande `ls` liste le contenu du dossier courant. Ici, elle est appelée avec le flag `-l`, pour le format long. Plus d'informations [ici](http://manpagesfr.free.fr/man/man1/ls.1.html).
Et bien, sachez qu'il est possible de créer des flags similaires en Go, et ce très simplement !

---

### Le package `flag`
Comme vous le savez sans doute, Go fournit un nombre impressionnant de packages, et parmi ceux-ci se trouve le package `flag`. Je vous le donne en mille, c'est lui qui nous intéressera au cours de cet article. En effet, c'est grâce à ce package que nous allons créer (simplement, je le rappelle) nos flags personnalisés.

À titre d'exemple, nous allons prendre la commande `head`, qui permet d'afficher les premières lignes d'un fichier. Sachez qu'elle accepte, de manière optionnelle, le flag `-n`, permettant de spécifier le nombre de lignes à afficher.
Par exemple, `head -n 5 foo.txt`, affichera les 5 premières lignes du fichier "foo.txt".

Retour sur Go : comment pouvons-nous implémenter la lecture d'un flag `-n` dans notre application ?
Le package `flag` fournit un ensemble de fonctions permettant de lire différents types de données, telles que :
```go
func Bool(name string, value bool, usage string) *bool
func BoolVar(p *bool, name string, value bool, usage string)

func Int(name string, value int, usage string) *int
func IntVar(p *int, name string, value int, usage string)

func String(name string, value string, usage string) *string
func StringVar(p *string, name string, value string, usage string)
```
***Note** - Bien d'autres fonctions sont fournies, je vous renvoie à [l'index du package](https://golang.org/pkg/flag/#pkg-index).*

Chacune de ces fonctions suit la logique suivante :
```go
func NomDuType(<Nom du flag>, <Valeur par défaut>, <Description>)
```

De plus, comme vous l'aurez constaté, les fonctions `Bool`, `Int` et `String` ont leur équivalent suffixé de `Var`, comme `IntVar`. Nous reviendrons dessus dans un instant. Pour le moment, essayons de créer un flag `-n`, à la manière de `head`.

Une autre fonction clé du package `flag` est la fonction `Parse()`. C'est elle qui est chargée d'interpréter les différents flags fournis à notre application.

Au vu de la manière dont fonctionnent, les diverses fonctions présentées ci-dessus, une première tentative pourrait être la suivante :
```go
func main() {
  n := flag.Int("n", 0, "description de ce super flag")
  flag.Parse()
  // La fonction 'Int' renvoie un pointeur (amis du C, bonjour).
  // Il nous suffit de le déréférencer pour obtenir sa valeur :
  fmt.Println("n =", *n)
}
```
Essayons de passer un flag à notre application : `go run main.go -n 5`... Et ça marche ! Excellent !

Revenons à présent sur les fonctions `BoolVar`, `IntVar` et leurs équivalents. Leurs fonctionnement est (très) légèrement différent. Plutôt que de renvoyer une valeur, elles prennent un argument supplémentaire en première position et suivent la logique suivante :
```go
func NomDuTypeVar(<Adresse du résultat>, <Nom du flag>, <Valeur par défaut>, <Description>)
```
Dans le cas de notre ami `-n`, l'usage serait donc celui-ci :
```go
func main() {
  var n int
  flag.IntVar(&n, "n", 0, "description de ce super flag")
  flag.Parse()
  fmt.Println("n =", n)
}
```

---

### Bonne pratique : la fonction `init()`
