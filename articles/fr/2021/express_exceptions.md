## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) EXPRESS #4 - Les exceptions, vraiment la meilleure solution ?

###### Note importante
> Cet article fait partie de la série "EXPRESS". Il s'agit d'une série d'articles relativement courts et moins formels, axés autour d'un point sur lequel je ne me voyais pas écrire un article complet. Le ton y est volontairement plus léger afin d'en faciliter la lecture (et l'écriture !).

---

### Avant-propos

Je sais d'avance que je m'aventure sur un terrain glissant en écrivant cet article.
Etant conscient que beaucoup de développeurs n'aiment pas voir leurs langages préférés critiqués ou leurs habitudes bousculées, je m'attends à ce que beaucoup soient en désaccord partiel avec cet article.

Par ailleurs, ce dernier n'expose en rien une vérité absolue, mais plutôt un avis personnel et purement individuel.

Enfin, à tous les amateurs de C# (que je vais prendre comme "mauvais" exemple) : soyez ouverts ! Je suis moi-même développeur C# de métier, cela ne me dispense pas d'avoir un avis critique sur ce langage !

---

### Introduction

Dans la plupart des langages courants, il existe la notion d'exception. Il s'agit d'un mécanisme destiné à "gérer" les cas inattendus, comme par exemple en C# :
```cs
// simple fonction visant à diviser deux nombres
int Divide(int a, int b) {
    if (b == 0) {
        throw new DivideByZeroException();
    }
    return a / b;
}
```
L'usage d'une exception se fait de manière claire, et son type (ici `DivideByZeroException`) est souvent suffisamment expressif pour avoir une idée du problème sous-jacent.
Toutefois, cela implique que le code appelant devrait théoriquement être proche de :
```cs
try {
    // ...
    int result = Divide(a, b);
    // ...
} catch (Exception e) {
    // gestion de l'erreur
}
```
Ainsi, si une erreur survient, nous sommes capables de l'écrire quelque part (un fichier de log, par exemple), d'utiliser une valeur par défaut, ou encore... de ne rien faire (vous savez, le fameux "bof, on verra plus tard" :D). Car en effet, rien ne nous oblige à "catcher" une exception ; c'est là une des failles du système.

---

### Les problèmes posés par les exceptions
À titre personnel, je rencontre régulièrement des exceptions provenant de couches logicielles bien plus basses que le code où je me trouve (notamment avec des bibliothèques tierces).
Ainsi, il faut aller fouiller dans le code source (quand il est disponible) pour comprendre le problème d'origine.

C'est une perte de temps sans intérêt due au fait que l'on emploie des exceptions pour tout et n'importe quoi. Il y a des cas où l'on n'a pas d'autre choix, et d'autres où ce n'est aucunement pertinent. En C#, [`string.Replace`](https://docs.microsoft.com/fr-fr/dotnet/api/system.string.replace?view=net-5.0) est un excellent cas :
```cs
var result = "Hello, world!".Replace("", "foo");
```
Ce code lèvera une `ArgumentException` alors qu'il pourrait juste... ne rien faire.
Cela induit que l'on *doit* prévoir *le* cas où la chaîne de caractères est vide ; ainsi, si elle provient d'un fichier, d'une entrée utilisateur ou autre, il *faut* préalablement prévoir *ce cas précis* pour ne pas lever d'exception. Alors que, je le rappelle, l'appel à `Replace` pourrait juste ne *rien* faire si la chaîne est vide ou nulle. C'est là l'un des nombreux cas d'usage excessif d'une exception.

***Note -** L'exemple de `string.Replace` a été trouvé en fouillant dans la doc pendant, environ... une minute. Il y a donc très probablement bien d'autres cas, mais celui-ci est à la fois évocateur et simple.*

Par ailleurs, lever une exception nécessite d'en faire quelque chose par la suite. Pour ce faire, on utilise un bloc `try`/`catch` qui, en plus d'être syntaxiquement lourd, est... facultatif. Ainsi, si l'on ne sait pas qu'une portion de code peut lever une exception (un appel à `string.Replace` par exemple ?), alors on s'expose à voir notre petite exception remonter sagement toutes les couches d'appels de notre code jusqu'à être rattrapée (ou pas !).
Et s'il s'agit d'une bibliothèque tierce dont le code est fermé... amusez-vous bien.

Car un autre des soucis d'une exception est qu'elle peut survenir *n'importe quand* : toute fonction ou méthode (selon le langage) peut théoriquement lever une exception. Or, pour des questions évidentes de debugging, il semble déjà plus intéressant de jouer sur la valeur de retour.

Sans pour autant revenir aux code d'erreurs utilisés en C, il existe d'autres solutions, à mon sens bien plus appropriées, à la gestion des cas inattendus.

---

### Solutions
En Go, nous avons le type `error`. Combiné à la possibilité de retourner plusieurs valeurs, il apporte plusieurs avantages :
- Si une fonction renvoie une `error`, alors on *sait* qu'elle est susceptible d'échouer : il n'y a alors aucun doute possible ;
- Cela nous incite à traiter l'erreur *immédiatement*, car c'est notre *seule* opportunité pour le faire : elle ne remontera pas la call stack !

Exemple :
```go
func ReadFromFile(path string) (string, error) {
    // l'implémentation ne nous intéresse pas ici
}

// et à l'usage :
content, err := ReadFromFile("input.txt")
if err != nil {
    // gestion de l'erreur...
}
```
Dans d'autres langages (je pense notamment aux langages fonctionnels), il existe les types `Result` et `Option` (ou `Maybe`, son équivalent en Haskell, Elm, ...).
Le type `Result` expose deux variants, `Ok` et `Err`, pour gérer les différents cas possibles.

Si l'on voulait réécrire notre lecture de fichier en Rust (par exemple), cela pourrait être proche de :
```rust
let result = read_from_file("input.txt");
match result {
  Ok(content) => // on fait quelque chose du contenu
  Err(error)  => // on fait quelque chose de l'erreur
}

// en étant joueur, on peut même imaginer un résultat personnalisé !
enum FileError {
  Unexisting,
  Locked,
}
type FileResult = Result<String, FileError>;
```
***Note -** La structure `match` est un peu comme un `switch`/`case` ([mais en mieux](https://doc.rust-lang.org/book/ch18-03-pattern-syntax.html)).*

Là encore, le type `Result` implique que nous *devons* traiter le cas immédiatement.

Le type `Option` a un comportement relativement proche. Il propose deux variants, `Some` et `None` pour matérialiser la présence ou l'absence de valeur. Voyez-le comme une boite : vous devez d'abord l'ouvrir pour voir ce qu'il y a dedans !
```rust
let some_int: Option<i32> = Some(42);
let none_int: Option<i32> = None;

match some_int {
    Some(x) => println!("x vaut {}", x),
    None    => println!("rien :("),
}
```
Une fonction `divide`, telle que l'exemple au début de l'article pourrait très bien renvoyer une `Option` plutôt que de lever une exception !

Implémentation en OCaml (pour changer de Rust) :
```ocaml
let divide a = function
    | 0 -> None
    | b -> Some(a/b)
```
Clair comme de l'eau de roche, non ? D'accord, trève de plaisanteries, voici une implémentation plus lisible (en Rust du coup) :
```rust
fn divide(a: i32, b: i32) -> Option<i32> {
    if b == 0 {
        return None;
    }
    Some(a/b)
}
```
On peut ensuite tester sa valeur avec un `match`, comme vu préalablement.

---

### Conclusion
Les exceptions sont utiles pour gérer certains cas inattendus, tels que l'appel d'une méthode sur une instance nulle, par exemple.
Néanmoins, leur utilisation à outrance est souvent la source de longues heures de debugging pour débusquer la source d'une exception provenant des tréfonds de l'application.

Fort heureusement, beaucoup de langages exposent des mécanismes proposant (ou imposant !) un circuit plus court entre l'apparition d'une erreur et son traitement. De prime abord, cela nous rajoute du boulot (car on *doit* gérer l'erreur), mais en définitive nous épargne du temps de debug ! Ce faisant, on peut le mettre à profit de manière plus productive !

Je suis conscient qu'en réalité, c'est bien plus complexe que cela. En entreprise, les développeurs sont contraints de respecter des deadlines afin de livrer dans les temps, parfois (souvent ?) au détriment de la qualité. Dans un monde idéal, il incomberait aux décideurs de donner davantage de temps en amont afin d'en économiser à moyen et long termes. Oui, ça paraît contre-intuitif dit comme ça, mais ça ne l'est pas tant que ça, car un produit correctement conçu fera l'objet de gains de temps considérables en debug !
