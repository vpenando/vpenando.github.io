## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) EXPRESS #2 - Du bon usage de `godoc`

###### Note importante
> Cet article fait partie de la série "EXPRESS". Il s'agit d'une série d'articles relativement courts et moins formels, axés autour d'un point sur lequel je ne me voyais pas écrire un article complet. Le ton y est volontairement plus léger afin d'en faciliter la lecture (et l'écriture !).

---

Bonjour !

Si vous êtes ici, c'est que vous avez lu la note en début de page (a priori juste au-dessus), et que vous ne vous attendez par conséquent pas à un article très formel comme ce que j'ai l'habitude de publier sur ce blog.

Au court de ce bref article, je vais présenter l'outil de génération de doc inclus avec Go, et comment l'utiliser. Rien de bien sorcier, vous verrez !

<img src="images/gopher.png" />

---

### La documentation à l'ère du jurassique
Il y a fort longtemps, lorsque la terre n'abritait pas encore l'humanité, écrire une doc était très fastidieux. Déjà parce que les dinosaures ne savaient pas écrire, mais surtout parce que l'on n'avait pas d'outils vraiment taillés pour.
Puis virent le jour des outils tels que [Doxygen](http://www.doxygen.nl/), permettant de générer une documentation à partir des commentaires contenus dans vos fichiers source.

---

### Et aujourd'hui ?
Certains langages de programmation (car c'est ce qui nous intéresse, dans le cas présent) embarquent des outils de génération de doc. C'est le cas de Go, dont nous allons parler ici.

Go, né il y a une petite dizaine d'années, est le langage de programmation créé par Google pour répondre aux problématiques inhérentes à Google. Il a ensuite été démocratisé au fil des années, jusqu'à se hisser parmi [les langages les plus populaires](https://hackr.io/blog/best-programming-languages-to-learn-2020-jobs-future).

Sa grande popularité s'explique par sa simplicité et son expressivité. Ces qualités font partie de l'ADN de Go, et son outil de génération de doc suit la même philosophie.

---

### Mise en pratique
Avant tout, créons une fonction quelconque :
```go
func Quelconque() {
    // ...
}
```
Ajoutons-y quelques ingrédients, tels que des paramètres :
```go
func Quelconque(param1 int, param2 string) {
    // ...
}
```
Enfin, implémentons-la !
```go
func Quelconque(param1 int, param2 string) {
    for i := 0; i < param1; i++ {
        fmt.Println(param2)
    }
 }
 ```
À présent, nous allons nous atteler à documenter cette fonction "à la Go". Cet outil fonctionne un peu comme Doxygen, énoncé plus haut ; tout se base sur les commentaires. Néanmoins, le commentaire doit commencer par le nom de la fonction.
```go
// Quelconque affiche N fois une chaîne de caractères donnée.
func Quelconque(param1 int, param2 string) {
    for i := 0; i < param1; i++ {
        fmt.Println(param2)
    }
}
```
Pour insérer du code dans notre doc, il suffit simplement de le précéder d'un espace :
```
ceci n'est pas du code
 ceci est du code
```
À l'usage :
```go
// Quelconque affiche N fois une chaîne de caractères donnée.
// Exemple d'utilisation :
//  Quelconque(42, "Bonjour !")
func Quelconque(param1 int, param2 string) {
    for i := 0; i < param1; i++ {
        fmt.Println(param2)
    }
}
```
