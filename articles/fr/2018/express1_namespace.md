## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) EXPRESS #2 - `using namespace`, vraiment une mauvaise pratique ?

###### Note importante
> Cet article est le second de la lignée "EXPRESS". Il s'agit d'une série d'articles relativement courts et moins formels, axés autour d'un point sur lequel je ne me voyais pas écrire un article complet. Le ton y est volontairement plus léger afin d'en faciliter la lecture (et l'écriture !).

---

Tout comme beaucoup de langages, C++ est bien fait. La stdlib est vaste, et tout est bien rangé dans le namespace `std`.
Sauf que des fois, on aimerait bien ne pas avoir à tout préfixer à coups de `std::`. Avoir une fonction qui découpe une `std::string` en `std::vector<std::string>` et les affiche via `std::cout`, ça commence à faire beaucoup de `std::` quand même.
Là où certains puristes menaceraient de vous casser les genoux si vous utilisez `using namespace std` (ou autre namespace), je vous propose des solutions alternatives et des cas d'usage où cette instruction est tolérée.

Tout d'abord, une chose simple : **JAMAIS de `using namespace XXX;` dans un header**. Sous **aucun** prétexte. Si vous importez le namespace `std` dans un header, et que le code client décide de créer une classe, par exemple `string`, que va-t-il se passer ?

Cela étant dit, j'ai souvent lu et entendu çà et là qu'il était tout aussi dangereux d'en faire de même dans un fichier source. Je ne suis pas d'accord avec cette affirmation, et je vais l'expliquer dans ce petit article.

---

A mon sens, il n'est absolument pas idiot d'utiliser un namespace dans un fichier source. Car il n'y a aucun risque d'affecter le code client de cette manière.


```cpp
// Dans un fichier .cpp :
namespace fs = boost::filesystem;
```

```cpp
// Dans une fonction :
void print_words(std::string const& text) {
  using Word  = std::string;
  using Words = std::vector<Word>;
  Words words = split(text); // Admettons qu'une telle fonction existe
  for (auto const& word : words) {
    std::cout << word << std::endl;
  }
}
