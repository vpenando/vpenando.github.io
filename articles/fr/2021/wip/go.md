## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Retour d'expérience sur Go

---

### Introduction
Après quelques années de pratique sur Go, et quasi quotidiennement ces derniers mois, je souhaiterais faire un petit retour d'expérience sur Go ;
aborder ses points forts, ses points faibles, et dresser un modeste bilan sur mon utilisation de ce langage.

---

### Points forts de Go
Avant tout, quels sont les points forts de Go ? Ces derniers ne manquent pas, et sont pour certains plus que significatifs.

**1. Go, un langage compilé**
Go est non seulement un langage compilé (à différencier de langages *interprétés*, comme la plupart des langages de script par exemple).
En tant que tel, il apporte certaines vérifications au *compile time*, permettant ainsi d'offrir des garanties supplémentaires plutôt que de planter de manière inattendue à l'exécution.
De plus, étant compilé en langage machine, il offre des performances presque comparables à du C ou du C++.

**2. Go, un langage statiquement typé**
Go est dit statiquement typé. Cela signifie qu'une variable a *un et un seul* type.
Il n'est théoriquement (\*) pas possible d'assigner à une même variable des valeurs de différents types.
Ainsi, pas de risque de surprise à l'évaluation d'une variable.

*(**\***) Il existe une exception, le type `interface{}`, détaillée dans la partie "points faibles de Go".*

**3. Une syntaxe simple et lisible**
Go a une syntaxe à la fois simple et très lisible.
La lecture d'un code Go est extrêmement fluide et sa compréhension se fait sans aucune difficulté.
À titre personnel, je n'ai au aucun problème à comprendre le code source de langage ou de certaines bibliothèques tierces lorsque j'en ai eu besoin.
Ce qui peut paraître anodin est l'ordre dans lequel on va lire un du code Go :
```go
func foo(a int, b int) int {
    // ...
}
```
On peut aisément lire (dans le sens de lecture occidental) *"la fonction `foo` attend un paramètre `a` de type `int`, un paramètre `b` de type `int` et renvoie un `int`"*.

- goroutines
- gestion des dépendances

---

### Points faibles de Go

- syntaxe ?
- conventions de nommage
- simplicité = limitations (génériques, const correctness, ...)
- gc ?
- le type `interface{}`
- `nil`

---

### Conclusion
