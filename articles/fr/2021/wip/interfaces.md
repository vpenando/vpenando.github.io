## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) EXPRESS #4 - POO : Du bon usage des interfaces

---

### Introduction

----

### 
Ne pas manipuler de types abstraits au plus bas niveau d'implémentation (pas de variables locales, par ex) :
```cs
IEnumerable<string> myStrings = new List<string>();
```
Ici, ce n'est **pas utile**, c'est même pénalisant ! Pas de `myStrings.Add` dispo dans `IEnumerable` !
À cet effet, ne pas renvoyer de type abstrait :
```cs
IEnumerable<string> Foo() {
    // ...
 }
 
 // ...
 var myStrings = this.Foo(); // Non !
 ```
 Un cast est alors nécessaire pour correctement manipuler `myStrings` !

---

### Cas d'usages
- LSP only

