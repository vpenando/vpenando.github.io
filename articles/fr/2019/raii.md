## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Le RAII, qu'est-ce que c'est ?

---

### Sommaire


---

### Définition

Le RAII (pour **R**esource **A**cquisition **I**s **I**nitialisation) est un principe clé de l'orienté objet. Malgré son nom trompeur, le RAII garantit que losqu'un object acquiert une ressource (pointeur, connexion, ...), ladite ressource sera bel et bien libérée lors de la destruction de l'objet.

Par exemple, en C++, `std::istream` et `std::ostream` sont, au même titre que `std::unique_ptr` et `std::shared_ptr`, des capsules RAII-conform. Les ressources allouées sont libérées à la destruction des instances.

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
