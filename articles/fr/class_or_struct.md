## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### [FR] POO - Classe ou structure ? Une histoire de sémantique

###### Note importante
> Cet article est une réflexion personnelle. En tant que telle, elle n'énonce pas une vérité absolue, mais plutôt ma manière de penser, et par extension d'écrire du code.

---

En programmation orientée objet, nous avons bien souvent le choix entre deux types d'entités lorsque nous voulons créer de nouveaux types : les **classes** et les **structure**.
Au delà de différences de visibilité -que je vais énoncer dans la partie suivante-, elles permettent d'apporter une sémantique supplémentaires à nos objet.

### 1. Différences fondamentales
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
Un tel code n'est possible avec une classe :
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

### 2. Différences sémantiques - Entité ou valeur ?
En programmation orientée objet, il existe différents piliers permettant d'assurer une minimum de cohérence conceptuelle, comme par exemple les principes [SOLID](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design)) ou encore la [loi de Déméter](https://fr.wikipedia.org/wiki/Loi_de_D%C3%A9m%C3%A9ter).
