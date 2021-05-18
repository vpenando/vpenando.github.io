## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) EXPRESS #4 - Les exceptions, vraiment la meilleure solution ?

###### Note importante
> Cet article fait partie de la série "EXPRESS". Il s'agit d'une série d'articles relativement courts et moins formels, axés autour d'un point sur lequel je ne me voyais pas écrire un article complet. Le ton y est volontairement plus léger afin d'en faciliter la lecture (et l'écriture !).

---

### Introduction

Dans la plupart des langages courants, il existe la notion d'exception. Il s'agit d'un mécanisme destiné à "gérer" les cas inattendus, comme par exemple :
```cs
// simple fonction visant à diviser deux nombres
int divide(int a, int b) {
    if (b == 0) {
        throw new DivideByZeroException();
    }
    return a / b;
}
```
Cela implique que le code appelant devrait théoriquement être proche de :
```cs
// ...
try {
    int result = divide(a, b);
} catch (Exception e) {
    // gestion de l'erreur
}
```
Ainsi, si une erreur survient, nous sommes capables de l'écrire quelque part (un fichier de log, par exemple), d'utiliser une valeur par défaut, ou encore... de ne rien faire.
Car rien ne nous oblige à "catcher" une exception ; c'est là une des failles du système.

---

### Les problèmes posés par les exceptions
Le problème d'une exception, c'est que si l'on ne la "catche" pas, elle peut soit faire crasher le programme,
soit se retrouver dans un `try`/`catch` d'une couche supérieure où elle n'a plus aucun sens.
À titre personnel, je rencontre régulièrement des exceptions provenant de couches logicielles bien plus basses que le code où je me trouve (notamment avec des bibliothèques tierces).
Ainsi, il faut aller fouiller dans le code source (quand il est disponible) pour comprendre le problème d'origine.

C'est une perte de temps sans intérêt due au fait que l'on emploie des exceptions pour tout et n'importe quoi. Il y a des cas où l'on n'a pas d'autre choix (en C# par exemple, où c'est la seule option disponible), et d'autres où ce n'est aucunement pertinent. Toujours en C#, [`string.Replace`](https://docs.microsoft.com/fr-fr/dotnet/api/system.string.replace?view=net-5.0) est un excellent cas :
```cs
var result = "Hello, world!".Replace("", "foo");
```
Ce code lèvera une `ArgumentException` alors qu'il pourrait juste... ne rien faire.
Cela induit que l'on *doit* prévoir *le* cas où la chaîne de caractères est vide ; ainsi, si elle provient d'un fichier, d'une entrée utilisateur ou autre, il *faut* préalablement prévoir *ce cas précis* pour ne pas lever d'exception. Alors que, je le rappelle, l'appel à `Replace` pourrait juste ne *rien* faire si la chaîne est vide ou nulle. C'est là l'un des nombreux cas d'usage excessif d'une exception.

Par ailleurs, lever une exception nécessite d'en faire quelque chose par la suite. Pour ce faire, on utilise un bloc `try`/`catch` qui, en plus d'être syntaxiquement lourd, est... facultatif. Ainsi, si l'on ne sait pas qu'une portion de code peut lever une exception (un appel à `string.Replace` par exemple ?), alors on s'expose à voir notre petite exception remonter sagement toutes les couches d'appels de notre code jusqu'à être rattrapée (ou pas !). Ainsi, s'il s'agit d'une bibliothèque tierce dont le code est fermé... amusez-vous bien.

Sans pour autant revenir aux code d'erreurs utilisés en C, il existe d'autres solutions, à mon sens bien plus appropriées, à la gestion des cas inattendus.

---

### Solutions
En Go, nous avons le type `error`. Combiné à la possibilité de retourner plusieurs valeurs, il apporte plusieurs avantages :
- Si une fonction renvoie une `error`, alors on *sait* qu'elle est susceptible d'échouer : il n'y a alors aucun doute possible ;
- Cela nous incite à traiter l'erreur *immédiatement*, car c'est notre *seule* opportunité pour le faire : elle ne remontera pas la call stack !
Exemple :
```go
func WriteToFile(path string, content string) error {
    // l'implémentation ne nous intéresse pas ici
}

// et à l'usage :
err := WriteToFile("output.txt", "Salut à toi qui liras ce fichier !")
if err != nil {
    // gestion de l'erreur...
}
```

- `error` en Go
- `Result` en OCaml, Rust, Haskell, Elm...

- lourd (try/catch/finally)
- solution par défaut pour tout et n'importe quoi
- aucune sûreté

