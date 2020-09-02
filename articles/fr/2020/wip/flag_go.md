## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) La gestion des flags en Go

---

### Sommaire
* [Introduction](#introduction)
* [Le package `flag`](#le-package-flag)
* [Bonne pratique : la fonction `init()`](#bonne-pratique--la-fonction-init)
* [Conclusion](#conclusion)

---

### Introduction
Les habitués de Linux connaissent sans doute la notion de *flag*. Il s'agit d'un moyen de passer des arguments à une application, notamment depuis un terminal.

Exemple :
```sh
ls -l
```
La commande `ls` liste le contenu du dossier courant. Ici, elle est appelée avec le flag `-l`, au sujet duquel vous trouverez plus d'informations [ici](http://manpagesfr.free.fr/man/man1/ls.1.html).
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

// ...
```
***Note** - Bien d'autres fonctions sont fournies, je vous renvoie à [l'index du package](https://golang.org/pkg/flag/#pkg-index).*

Chacune de ces fonctions suit la logique suivante :
```go
func NomDuType(<Nom du flag>, <Valeur par défaut>, <Description>)
```
Mais, me direz-vous, quel intérêt de fournir une description à un flag ? En effet, à quel moment est-elle utilisée ? Imaginez une fonction d'aide, permettant de lister et décrire vos différents flags. Avoir une description est toujours utile. Mais, comme il serait fastidieux de créer cette fonction soi-même (et la mettre à jour quand on ajoute ou supprime des flags), il existe la fonction `Usage()`, qui fait tout ça pour vous.

De plus, comme vous l'aurez constaté, les fonctions `Bool`, `Int` et `String` ont leur équivalent suffixé de `Var`, comme `IntVar`. Nous reviendrons dessus dans un instant. Pour le moment, essayons de créer un flag `-n`, à la manière de `head`.

Une autre fonction clé du package `flag` est la fonction `Parse()`. C'est elle qui est chargée d'interpréter les différents flags fournis à notre application.

Au vu de la manière dont fonctionnent les diverses fonctions présentées ci-dessus, une première tentative pourrait être la suivante :
```go
func main() {
    n := flag.Int("n", 0, "description de ce super flag")
    flag.Parse()
    // La fonction 'Int' renvoie un pointeur (amis du C, bonjour).
    // Il nous suffit de le déréférencer pour obtenir sa valeur :
    fmt.Println("n =", *n)
}
```
Essayons de passer un flag à notre application : `go run main.go -n 5`... Et ça marche ! Excellent ! Une syntaxe alternative existe, à savoir `go run main.go -n=5`. De plus, pour les booléens, nul besoin de spécifier une valeur ; en effet, celle-ci est par défaut conditionnée par la présence ou l'absence du flag.

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
Il semble de prime abord moins pratique que la version précédente ; en effet, quel intérêt de passer par un paramètre supplémentaire plutôt que d'attendre une valeur de retour ? C'est la question à laquelle nous allons répondre dans la partie suivante.

---

### Bonne pratique : la fonction `init()`
La fonction `init()` est une fonction un peu particulière, un peu à la manière de `main()`. Tentez d'exécuter ce code :
```go
func init() {
    fmt.Println("Never called...?")
}

func main() {
}
```
([Exemple en ligne](https://play.golang.org/p/wth4TMNhcAb))

Que s'est-il passé !? Nous n'avons pas appelé `init()`, et pourtant, elle s'est exécutée ? En effet, cette fonction, si elle est déclarée, est appelée automatiquement. Mieux encore, tout fichier source peut déclarer une fonction `init()`... et même plusieurs ! Davantage d'informations [ici](https://golang.org/doc/effective_go.html#init).

Cette fonction est donc très utile pour effectuer des traitements divers en amont de la logique du programme. Des traitement tels que, disons, la lecture de flags, par exemple ! Et en prime, c'est là qu'entrent sérieusement en jeu nos fonctions `IntVar`, `StringVar` et leurs cousines !

Exemple :
```go
var (
    n int
    b bool
    s string
)

func init() {
    // La lecture des flags est effectuée ici :
    flag.IntVar(&n, "n", 0, "description")
    flag.BoolVar(&b, "b", false, "description")
    flag.StringVar(&s, "s", "empty", "description")
    flag.Parse()
}

func main() {
    // Nous n'avons plus qu'à traiter les valeurs de nos différents flags :
    fmt.Println("n =", n)
    fmt.Println("b =", b)
    fmt.Println("s =", s)
}
```
([Exemple en ligne](https://play.golang.org/p/xMh6ngRG4Az))

En utilisant la fonction `init()` de la sorte, nous séparons la lecture de nos différents flags et le traitement de leurs valeurs.

---

### Conclusion
Go est un langage extrêmement puissant, qui fournit une suite d'outils et de packages très impressionnante afin de répondre à la plupart des besoins. Parmi eux, le package `flag` permet une gestion des flags extrêmement simple : en effet, en seulement deux lignes, nous pouvons ajouter un flag à notre application. Et, comme si ça ne suffisait pas, il existe la fonction `Usage()`, qui permet de lister les flags que nous avons créés.
De plus, en utilisant la fonction `init()`, nous pouvons segmenter notre code de manière à isoler la lecture des flags sans polluer notre code.
