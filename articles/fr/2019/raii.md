## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Le RAII, qu'est-ce que c'est ?

---

### Sommaire


---

### Définition

Je parle souvent de cette notion dans mes articles, mais je ne l'ai pas encore définie (du moins, sur ce blog).

Le RAII (pour **R**esource **A**cquisition **I**s **I**nitialisation) est un principe clé de l'orienté objet. Malgré son nom trompeur, le RAII garantit que losqu'un object acquiert une ressource, ladite ressource sera bel et bien libérée lors de la destruction de l'objet. Il associe la durée de vie d'une ressource à celle de son propriétaire.

Définissons avant tout ce que peut être une ressource. Dans notre cas (et surtout en C++, ce dernier étant mon langage de prédilection), il peut s'agir de :
* Pointeurs bruts (`int*`, `char*`, ...) ;
* Stream vers un fichier ;
* Connexion SQL ;
* ...

Le point commun de ces éléments est qu'ils doivent être à la fois alloués et libérés à la main. Le but du RAII est de s'affranchir de ces étapes en les rendant automatiques.

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
Dans un exemple aussi simple, le fichier sera libéré comme attendu, mais dans un exemple plus complexe *-ou pire, dans un code mêlant C et C++-*, il peut ne **jamais** l'être. Il y a alors une fuite mémoire. Le RAII est le meilleur moyen de se prémunir des fuites mémoire, en garantissant que les capsules ayant l'ownership s'occupent de libérer les ressources :
```cpp
std::ifstream stream{"toto.txt"};
if (stream) {
  // lecture du fichier...
  if (/* condition */) {
    throw std::runtime_error{"invalid input"};
  }
}
```
Dans cet exemple, si l'on atteint la condition permettant de lever une exception, le fichier sera correctement fermé, car le destructeur de `std::ifstream` sera appelé.

Les langages modernes sont à peu près full RAII-conform ; les objets sont généralement supprimés par un garbage collector, et les ressources sous-jacentes le sont également.

---

### Cas d'utilisation

D'une manière générale, il convient de *toujours* passer par des capsules RAII lorsque l'on veut manipuler des ressources qui doivent être allouées et libérées à la main. Usez et abusez d'objets RAII en C++ (`std::string`, `std::vector`, ...) plutôt que d'employer des pointeurs bruts. Toujours en C++, créez un wrapper RAII lorsque vous devez utiliser une bibliothèque C de manière à s'affranchir de multiples vérifications après chaque allocation.

Si les pointeurs bruts sont dépréciés en C++, ce n'est pas pour rien :
```cpp
int *ptr1 = new int(42);
int *ptr2 = new int(12); // potentielle fuite mémoire si ça throw
                         // car ptr1 ne sera jamais détruit
// utilisation
// ...

delete ptr1;
delete ptr2;
```
Version RAII-conform :
```cpp
auto ptr1 = std::make_unique<int>(42);
auto ptr2 = std::make_unique<int>(12);
// utilisation
```
