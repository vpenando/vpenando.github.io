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

a
