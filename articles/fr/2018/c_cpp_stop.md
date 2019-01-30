## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) "C/C++", stop !

---


### Introduction
A l'heure où j'écris ces lignes, nous sommes en 2018. Et aujourd'hui encore, en me baladant sur LinkedIn, je suis tombé sur une offre d'emploi où était recherché un "développeur C/C++". A l'auteur de cette offre, je lance la question suivante : développeur C, **ou** C++ ?
Car, si ces deux langages ont un héritage en commun, ils n'ont aujourd'hui plus grand chose à voir entre eux. J'entends souvent dire que "le C++, c'est juste du C avec de l'objet". Non. Du tout. Car cela induit que les templates, les exceptions, les références, les namespaces, les lambdas, l'inférence de type, le RAII... sont disponibles en C. Ce serait bien, ça en ferait sûrement un formidable outil aujourd'hui. Car en plus d'être puissant, il serait plaisant à utiliser. Mais malheureusement, non.
Enfin, ce n'est pas la question.

Je vous préviens d'avance : cet article sera un gros "coup de gueule". Je ne prétends pas être un excellent programmeur, mais je sais différencier du code acceptable à du mauvais code. Or, mélanger C et C++ produit généralement du mauvais code.


---


### Source du problème
C et C++ sont aujourd'hui plus différents que jamais. Les mélanger est une très mauvaise idée et apprendre l'un avant l'autre peut être contre-productif. Trop de "vieux" développeurs ayant connu le "C with classes" pondent du code mélangeant les deux. A tel point que le monde du travail autour du C++ souffre de ce problème. Et de ce problème découle un autre problème, à savoir que l'enseignement n'évolue pas, ou très peu. Ca a été le cas pour mes études : on m'a enseigné un mélange de C et de C++, et j'ai dû apprendre de mon côté, en autodidacte, le C++ et les normes récentes, et "désapprendre" les âneries qui m'ont été enseignées. Au revoir les `char*`, au revoir les tableaux à base de pointeurs, au revoir les tableaux de tableaux (de tableaux), au revoir `malloc`, au revoir `free`... Et au revoir `new`/`delete` (surtout `delete`, car `new` peut être utilisé conjointement avec les pointeurs intelligents), ainsi que tout ce qui n'est pas RAII-conform. Car la feature la plus importante en C++, et dont il faut abuser, ce n'est pas la POO, les templates ou autre ; c'est le RAII. Il est naturel de laisser des bugs quand on programme, mais ceux de type fuite mémoire peuvent être très facilement évités en respectant le RAII.

Le mauvais enseignement du langage mène à des aberrations comme ces fameuses classes `Matrix` à base de double pointeurs [ne respectant pas le RAII](https://github.com/nkt/cpp-matrix/blob/master/Matrix.hpp) (le pointeur interne n'est jamais libéré) ou encore [ne respectant pas le SRP](https://github.com/hyominchoi/Cpp-matrix-class/blob/master/Matrix.h) (`printMatrix` : une matrice n'a pas à s'afficher elle-même, ce n'est pas *sa* responsabilité). Je passe sur les autres erreurs (tableau de tableau au lieu de linéariser un vector, `get`/`set` au lieu de `operator()`, opérateurs internes à la classe, aucune sécurisation des allocations... pourquoi `std::ostream::operator<<` friend ?). Car enseigner le C++ en tant que "C/C++" incite à utiliser des pointeurs comme en C. L'enseignement de l'orienté objet est également à revoir selon moi, mais je m'égare encore. J'en parlerai sûrement au cours d'un prochain article.


---


### Mélanger C et C++ : impacts
C++ souffre énormément de son héritage du C, dû au souhait de garder une rétrocompatibilité entre les deux. C'est ainsi que l'on se retrouve avec du code blindé de `#define`, de `char*` pour représenter des chaînes de charactères, de pointeurs bruts pour utiliser des tableaux, des `goto`... mais fort heureusement, on garde aussi quelques trucs utiles, comme la macro `assert`, très utilisée en programmation par contrat.

Néanmoins, beaucoup de programmeurs font ce que je juge être n'importe quoi en C++. Dans cette section, je souhaiterais montrer en quoi mélanger du C et du C++ peut être contre-productif, voire même nocif.

Exemple :

```cpp
class Foo final {
public:
  Foo(int i)
    : i_(i)  { /* ... */ }
  ~Foo()     { /* ... */ }
  void bar() { /* ... */ }
  void baz() { /* ... */ }
  
private:
  int i_;
};

// ...
Foo *ptr = (Foo*) malloc(sizeof(*ptr)); // Où est l'erreur ?
if (! ptr) {
  // Gestion de l'erreur
}
ptr->bar();
ptr->baz();
free(ptr);
```

Dans ce code, où est le problème ? J'alloue un pointeur vers une instance de `Foo`. A priori, tout va bien, non ? Et bien en fait, non, tout ne va pas bien, car le constructeur de `Foo` n'est jamais appelé quand on utilise `malloc` (même soucis avec le destructeur si l'on utilise `free`). Ainsi, l'instance vers laquelle pointe `ptr` n'est jamais dans un état cohérent. Le comportement du programme est donc indéterminé.

Sans compter que ce code n'est absolument pas RAII-conform. Une utilisation propre d'un pointeur en C++ ressemblerait à :
```cpp
auto ptr = std::make_unique<Foo>(42);
// Si l'allocation échoue, une exception est levée
// Ici, 'ptr' est dans un état cohérent
ptr->bar();
ptr->baz();
// Et après utilisation, 'ptr' est libéré correctement
```

Le code est ici plus élégant, plus court et plus sûr.

Un autre exemple de "C/C++" bancal est l'utilisation de tableaux :
```cpp
int tab[100];

// Ou, pire :

int *tab = (int*) malloc(100 * sizeof(int));
if (! tab) {
  // ...
}
// ...
free(tab);
```

L'un des soucis est que la taille du tableau doit être stockée dans une variable tierce, là où `std::vector` et `std::array` exposent une fonction membre `size`, ainsi que la possibilité de détecter les accès hors bornes si besoin. De plus, nul besoin de tester si l'allocation de notre `vector` s'est bien passée, et `size` renverra toujours la bonne valeur même si la taille du tableau change. Peut-on en dire autant d'un simple pointeur ?

---

### Solutions
Je pense qu'il est raisonnablement possible, et envisageable, de sensibiliser les développeurs au RAII et aux principes SOLID. En ce qui me concerne, j'ai eu l'occasion de tenir une première conférence sur le sujet en 2016. Elle a été bien accueillie par certains, moins bien par d'autres, mais elle a au moins eu le mérite de souveler la question et de faire réfléchir. Néanmoins, à mon âge, j'ai beaucoup de mal à convaincre les seniors. Car après tout, je ne suis qu'un petit jeune en début de carrière, mon avis est difficilement bien accuelli.

Une autre solution (que je n'ai pas creusée outre mesure) est une fonctionnalité de certains compilateurs, qui consiste à "bannir" l'utilisation de certaines fonctions ([*poison*](https://github.com/leafsr/gcc-poison)) de manière à forcer les utilisateurs du code à employer une autre solution, plus sûre.

---

### Conclusion
Soyons clairs : le "C/C++" n'existe pas, dans le sens où ce n'est pas un langage. C'est un mélange maladroit de deux langages ayant deux optiques différentes, si ce n'est opposées. Le langage C est par nature radicalement opposé au RAII, l'un des plus importants pilliers du C++. Cet argument seul devrait être suffisant pour ne pas mélanger C et C++. Rajoutons à cela qu'utiliser du code C au sein d'un programme C++ (à part de rares cas comme l'utilisation d'`assert` ou l'écriture d'un wrapper RAII-conform d'une API C, [exemple](https://github.com/vpenando/sdl-cpp)) peut mener à la catastrophe si l'on ne sait pas ce que l'on fait.
