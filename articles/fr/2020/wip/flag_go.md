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

---

### Le package `flag`

---

### Bonne pratique : la fonction `init()`
