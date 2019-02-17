## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) La valeur nulle, un réel intérêt ?

---

### Sommaire


---

### Définition

---

### Exemples
Exemple de code non-conforme au RAII :
```c
FILE *file = fopen("foo.txt", "r");
if (! file) {
  // gestion de l'erreur
} else {
  // lecture du fichier...
  fclose(file);
}
```
