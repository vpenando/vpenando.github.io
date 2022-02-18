#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Le RAII, qu'est-ce que c'est ?

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

À présent, définissons ce qu'est un *propriétaire*. Lorsque l'on dit qu'un objet détient une ressource, cela signifie que l'objet dispose "sous le capot" d'une instance de ladite ressource. Par exemple, un `std::vector<T>` est propriétaire d'un `T*`, un `std::array<T, SIZE>` est propriétaire d'un `T[SIZE]`, et cela s'applique à la plupart des conteneurs de la STL.

Par exemple, en C++, `std::istream` et `std::ostream` sont, au même titre que `std::unique_ptr` et `std::shared_ptr`, des capsules RAII-conform. Les ressources allouées sont libérées à la destruction des instances.

---

### Exemples
Exemple de code non-conforme au RAII (car le fichier doit être libéré à la main) :
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

Il en va bien évidemment de même pour les pointeurs intelligents :
```cpp
auto ptr = std::make_unique<int>(42);
throw std::runtime_error{"une erreur quelconque"}; // 'ptr' est quand même libéré
                                                   // car le destructeur de std::unique_ptr<int>
                                                   // a été appelé.
```

Les langages modernes sont à peu près full RAII-conform ; les objets sont généralement supprimés par un garbage collector, et les ressources sous-jacentes le sont également.

---

### Cas d'utilisation

D'une manière générale, il convient de *toujours* passer par des capsules RAII lorsque l'on veut manipuler des ressources qui doivent être allouées et libérées à la main. Usez et abusez d'objets RAII en C++ (`std::string`, `std::vector`, ...) plutôt que d'employer des pointeurs bruts. Toujours en C++, créez un wrapper RAII lorsque vous devez utiliser une bibliothèque C de manière à vous affranchir de multiples vérifications après chaque allocation ; tout sera fait sous le capot, et le code véritablement utile ne sera pas "pollué" de vérifications :
```cpp
// ce qu'il ne faut PAS faire en C++
SDL_Window *window = SDL_CreateWindow(
    "Ma super fenêtre SDL",
    SDL_WINDOWPOS_UNDEFINED,
    SDL_WINDOWPOS_UNDEFINED,
    640,
    480,
    SDL_WINDOW_OPENGL
);
if (! window) {
    throw std::runtime_error{SDL_GetError()};
}

// utilisation
// ...

SDL_DestroyWindow(window);
```
Version RAII-conform :
```cpp
// meilleure approche
sdl::Window window{"Ma super fenêtre SDL", 640, 480, sdl::Window::OPENGL};

// utilisation
// ...

// rien à faire ! Le destructeur s'occupe de tout
```

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
// ...

// rien à faire ! Le destructeur s'occupe de tout aussi
```

---

### Conclusion

En somme, le RAII est le *meilleur* moyen de s'assurer d'une gestion correcte des ressources. Plus concis et surtout automatique, il lie intrinsèquement la vie d'une ressource à celle de son propriétaire. Par exemple, un `std::unique_ptr<int>` est propriétaire d'un `int*` et garantit que ce dernier sera correctement alloué et libéré quoi qu'il arrive.

