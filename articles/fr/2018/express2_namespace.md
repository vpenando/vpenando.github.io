## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) EXPRESS #2 - `using namespace`, vraiment une mauvaise pratique ?

###### Note importante
> Cet article est le second de la lignée "EXPRESS". Il s'agit d'une série d'articles relativement courts et moins formels, axés autour d'un point sur lequel je ne me voyais pas écrire un article complet. Le ton y est volontairement plus léger afin d'en faciliter la lecture (et l'écriture !).

---

Tout comme beaucoup de langages, C++ est bien fait. La stdlib est vaste, et tout est bien rangé dans le namespace `std`.
Sauf que des fois, on aimerait bien ne pas avoir à tout préfixer à coups de `std::`. Avoir une fonction qui découpe une `std::string` en `std::vector<std::string>` et les affiche via `std::cout`, ça commence à faire beaucoup de `std::` quand même.
Là où certains puristes menaceraient de vous casser les genoux si vous utilisez `using namespace std` (ou autre namespace), je vous propose des solutions alternatives et des cas d'usage où cette instruction est -selon moi- tolérable.

Tout d'abord, une chose simple : **JAMAIS de `using namespace XXX;` dans un header**. Sous **aucun** prétexte. Si vous importez le namespace `std` dans un header, et que le code client décide de créer une classe, par exemple `string`, que va-t-il se passer ?

Cela étant dit, j'ai souvent lu et entendu çà et là qu'il était tout aussi dangereux d'en faire de même dans un fichier source. Je ne suis pas tout à fait d'accord avec cette affirmation, et je vais l'expliquer dans ce petit article.

---

A mon sens, il n'est absolument pas idiot d'utiliser un namespace dans un fichier source. Car il n'y a aucun risque d'affecter le code client de cette manière.

Néanmoins, je vais tout de même nuancer ce propos. S'il n'y a aucun risque d'affecter le code client, il est possible d'affecter le vôtre. Aussi, il faut être sûr de ce que l'on fait et de ce que l'on va faire.

D'une manière générale, je propose deux manières alternatives pour réduire la verbosité de votre code sans pour autant risquer de générer des conflits.

Lorsque vous avez un namespace dont le nom est long, préférez un alias :
```cpp
// Dans un fichier .cpp :
namespace fs = std::filesystem;
// (Ou boost::filesystem si vous n'avez pas de compilateur supportant C++17)
```
L'intérêt est que l'on peut faire en sorte que `fs` soit un alias de `boost::filesystem` ou de `std::filesystem` (en fonction de la norme utilisée) à coups de `#if`. C'est un détail, mais c'est toujours bon à savoir.

On peut également importer sélectivement ce dont on a besoin :
```cpp
// De manière globale :
using std::cout;
using std::endl;

// ... Ou dans une fonction :
void print_words(std::string const& text) {
  using std::cout;
  using std::endl;
  const auto words = split(text); // Admettons qu'une telle fonction existe
  cout << "There's " << words.size() << " words here!" << endl;
  for (auto const& word : words) {
    cout << word << endl;
  }
}
```
Cette méthode propose l'avantage de pouvoir être utilisée dans une fonction définie dans un header (dans le cas d'une fonction `inline` par exemple), vu que son scope est la fonction elle-même.

Il est par ailleurs tout à fait viable d'importer un namespace dans une fonction :
```cpp
// Extrait d'un petit programme fait sur mon temps libre :

void mov(Register& dst, Address src) noexcept {
  using namespace constants; // MAX_ADDRESS, MIN_ADDRESS
  using namespace memory;    // RAM
  const auto addr = static_cast<Byte>(src);
  assert(addr <  MAX_ADDRESS);
  assert(addr >= MIN_ADDRESS);
  mov(dst, RAM[addr]);
}
```
Bien que cela ne soit pas nécessairement très utile (car après tout, je n'utiliserais qu'une fois `memory::` et deux fois `constants::`), j'utilise ces namespaces explicitement pour éviter d'avoir des instructions à rallonge.

Notez que j'anote tout de même en commentaire ce pour quoi j'importe chacun des namespaces concernés. Ainsi, je sais pour quoi j'en ai besoin.

---

