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
Exemple de code non-conforme au RAII car le fichier doit être libéré à la main :
```c
FILE *file = fopen("foo.txt", "r");
if (! file) {
  // gestion de l'erreur
} else {
  // lecture du fichier...
  fclose(file);
}
```
Dans un exemple aussi simple, le fichier sera libéré comme attendu, mais dans un exemple plus complexe *-ou pire, dans un code mêlant C et C++-*, il peut ne **jamais** l'être. Il y a alors une fuite mémoire. Le RAII est le meilleur moyen de se prémunir des fuites mémoire, en garantissant que les capsules ayant l'ownership s'occupent de libérer les ressources.

Exemple de code conforme au RAII :
```py
with open("foo.txt", "r") as f:
    # lecture du fichier
# ici, le fichier est fermé
```
