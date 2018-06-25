## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Du manager à l'entropie

---

#### Introduction
En programmation orientée objet, on est souvent tenté de créer des "managers", ces fameuses classes qui "gèrent des choses".
Par exemple, une classe devant "gérer" un fichier texte pourrait être exprimée ainsi :
```cpp
class FileManager {
public:
  explicit FileManager(std::string const& path);
  virtual ~FileManager();
  void create();
  bool exists() const;
  void write(std::string const& text);
  std::string read() const;
};
```

Ou, pire encore :
```cpp
class FileManager {
public:
  explicit FileManager();
  virtual ~FileManager();
  void create(std::string const& path);
  bool exists(std::string const& path) const;
  void write(std::string const& path, std::string const& text);
  std::string read(std::string const& path) const;
};
```
***Note** - La seconde version est encore pire car elle respecte encore moins le SRP du fait qu'une instance de cette classe est susceptible de "gérer" plusieurs fichiers.*

Nous sommes en droit de nous poser la question suivantes : *En quoi est-ce si mal de créer un "manager" ? Tant que mon entité doit gérer des choses, il semble tout à fait légitime, voire approprié, de l'appeler `XXXManager`, non ?*

---

#### Explication
En effet, une classe devant "gérer des choses" peut tout à fait être appelée "manager". Le problème ne se situe pas en ce point, mais plutôt en l'essence même de la classe. L'existence d'une telle entité mène, à terme, à une architecture bancale qui accélère un phénomène que l'on appelle l'**entropie du logiciel**.

Avant tout, présentons *en quoi* créer un `XXXManager` est une mauvaise idée. En POO, il convient de respecter a minima les [principes SOLID](https://en.wikipedia.org/wiki/SOLID). Dans notre cas, nous allons surtout nous intéresser au premier d'entre eux, à savoir le [SRP](https://en.wikipedia.org/wiki/Single_responsibility_principle), qui énonce la chose suivante :
> Toute fonction, classe, ou module, doit avoir une et une seule responsabilité. Cette entité doit rendre **un** service, et **bien** le rendre.
