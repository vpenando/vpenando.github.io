## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Les exceptions, vraiment la meilleure solution ?

---

### Sommaire

---

### <a name="introduction">Introduction</a>

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
soit se retrouver dans un `try`/`catch` d'une couche supérieur où elle n'a plus aucun sens.
À titre personnel, je rencontre régulièrement des exceptions provenant de couches logicielles bien plus basses que le code où je me trouve (notamment avec des bibliothèques tierces).
Ainsi, il faut aller fouiller dans le code source (quand il est disponible) pour comprendre le problème d'origine.

- lourd (try/catch/finally)
- solution par défaut pour tout et n'importe quoi
- aucune sûreté

