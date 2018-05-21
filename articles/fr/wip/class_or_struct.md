## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### [FR] POO - Classe ou structure ? Une histoire de sémantique

###### Note importante
> Cet article est une réflexion personnelle. En tant que telle, il n'énonce pas une vérité absolue, mais plutôt ma manière de penser, et par extension d'écrire du code.

---

### Sommaire
* [1. Différences fondamentales](#differences)
* [2. Différences sémantiques - Entité ou valeur ?](#semantics)

---

En programmation orientée objet, nous avons bien souvent le choix entre deux types d'entités lorsque nous voulons créer de nouveaux types : les **classes** et les **structures**.
Au delà de différences de visibilité -que je vais énoncer dans la partie suivante-, elles permettent d'apporter une sémantique supplémentaire à nos objets.

### <a name="differences">1. Différences fondamentales</a>
Les structures et les classes présentent deux différences majeures en termes de visibilité : là où par défaut tout est public dans une structure, tout est privé dans une classe. Exemple :
```cpp
struct Point {
  Point(int p_x, int p_y);
  int x;
  int y;
};
```
Dans cette structure, absolument **tout** est public. Si bien qu'il est possible de faire :
```cpp
Point pt{10, 12};
std::cout << "pt: (" << pt.x << ", " << pt.y << ")";
```
Et le résultat sera bien entendu :
```
pt: (10, 12)
```
Compiler un tel code n'est cependant pas possible avec une classe :
```cpp
class Point {
  Point(int p_x, int p_y);
  int x;
  int y;
};
```
```
main.cpp:10:9: error: calling a private constructor of class 'Point'
  Point pt{10, 12};
        ^
main.cpp:4:3: note: implicitly declared private here
  Point(int p_x, int p_y);
  ^
main.cpp:11:30: error: 'x' is a private member of 'Point'
  std::cout << "pt: (" << pt.x << ", " << pt.y << ")";
                             ^
main.cpp:5:7: note: implicitly declared private here
  int x;
      ^
main.cpp:11:46: error: 'y' is a private member of 'Point'
  std::cout << "pt: (" << pt.x << ", " << pt.y << ")";
                                             ^
main.cpp:6:7: note: implicitly declared private here
  int y;
      ^
3 errors generated.
```
Il en va de même avec l'héritage ; si l'héritage est par défaut public avec une structure, il devient privé en utilisant une classe.

**Note** - *Une structure héritant d'une classe en héritera par défaut de manière... publique !*

Ces différences de visibilité posent les bases d'une divergence sémantique majeure entre ces deux types d'entité.

### <a name="semantics">2. Différences sémantiques - Entité ou valeur ?</a>
En programmation orientée objet, il existe différents piliers permettant d'assurer un minimum de cohérence conceptuelle, comme par exemple les principes [SOLID](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design)) ou encore la [loi de Déméter](https://fr.wikipedia.org/wiki/Loi_de_D%C3%A9m%C3%A9ter).

Parallèlement à ces principes, il existe les notions de classes à **sémantique de valeur** et classes à **sémantique d'entité**. Cette différence est conditionnée par la réponse à la question suivante :
> Fait-il sens, à tout instant *t*, d'avoir deux instances identiques de cette classe ou structure ?

A titre d'exemple, pour la structure `Point` présentée plus haut, il est tout à fait sensé d'avoir deux instances identiques.
A contrario, l'utilisation d'une classe représentant une entité unique (un ID, un compte bancaire, un individu, ...) ne tend pas à créer deux instances identiques.

Une classe comme `Point` est ce que l'on appelle une classe à sémantique de **valeur**.

Une classe telle que `std::unique_ptr`, par exemple, serait ce que l'on appelle une classe à sémantique d'**entité**. En tant que telle, elle n'est pas copiable.

**Note** - *Il est relativement rare qu'une classe à sémantique de valeur soit à la base d'une relation d'héritage.*

**Note** - *D'une manière générale, une classe telle qu'un aggrégat de données aura sémantique de valeur. Exemples : `Point`, `Color`, `Square`, `Rectangle`...*

Et c'est là qu'entrent en jeu les classes et les structures. Lorsque je crée une classe à sémantique de valeur, j'utilise par défaut le mot-clé `struct`. Dans le cas d'une classe à sémantique d'entité, alors j'utiliserai le mot-clé `class`. Cela permet de savoir d'office à quel type d'entité nous avons affaire.
Bien entendu, cela n'est en rien une vérité absolue
