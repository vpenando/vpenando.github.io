## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) "C/C++", stop !

---

### Sommaire
* [Introduction](#introduction)



---

### Introduction
A l'heure où j'écris ces lignes, nous sommes en 2018. Et aujourd'hui encore, en me baladant sur LinkedIn, je suis tombé sur une offre d'emploi pour un "développeur C/C++". A l'auteur de cette offre, je lance la question suivante : développeur C, **ou** C++ ?
Car, si ces deux langages ont un héritage en commun, ils n'ont aujourd'hui plus grand chose à voir entre eux. J'entends souvent dire que "le C++, c'est juste du C avec de l'objet". Non. Du tout. Car cela induit que les templates, les exceptions, les références, les namespaces, les lambdas, l'inférence de type, le RAII... sont disponibles en C. Ce serait bien, ça en ferait sûrement un formidable outil aujourd'hui. Car en plus d'être puissant, il serait plaisant à utiliser. Mais malheureusement, non.
Enfin, ce n'est pas la question.

---

### Utiliser l'un ou l'autre, mais pas les deux
Si je veux faire du C, je fais du C. Si je veux faire du C++, je fais du C++.

C et C++ sont aujourd'hui plus différents que jamais. Les mélanger est une très mauvaise idée et apprendre l'un avant l'autre peut être contre-productif. Trop de ~~dinosaures~~ développeurs seniors pondent du code mélangeant les deux, ayant connu le "C with classes". A tel point que le monde du travail autour du C++ souffre de ce problème. Et de ce problème découle un autre problème, à savoir que l'enseignement n'évolue pas, ou très peu. Ca a été le cas pour mes études : on m'a enseigné un mélange de C et de C++, et ça l'est pour mon travail aujourd'hui.

---

### Mélanger C et C++ : impacts
C++ souffre énormément de son héritage du C, dû au souhait de garder une rétrocompatibilité entre les deux. C'est ainsi que l'on se retrouve avec du code blindé de `#define`, de `char*` pour représenter des chaînes de charactères, de pointeurs bruts pour utiliser des tableaux, des `goto`... mais fort heureusement, on garde aussi quelques trucs utiles, comme la macro `assert`, très utilisée en programmation par contrat.

Dans cette section, je souhaiterais montrer en quoi mélanger du C et du C++ pouvait être nocif. Exemple :

```cpp
class Foo {
public:
  Foo(int i) : i_(i) {}
  void bar() { /* ... */ }
  void baz() { /* ... */ }
};

// ...
Foo *ptr = (Foo*) malloc(sizeof(*ptr)); // Où est l'erreur ?
```
