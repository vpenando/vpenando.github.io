## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Le RAII, qu'est-ce que c'est ?

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

Exemple de code conforme au RAII :
```py
with open("foo.txt", "r") as f:
    # lecture du fichier
# ici, le fichier est fermé
```
