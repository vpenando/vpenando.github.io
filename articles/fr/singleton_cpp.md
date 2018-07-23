## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Réflexion - Implémentation d'un singleton en C++

###### Note importante
> Cet article provient de mon "ancien" dev blog, avec quelques petits remaniements. Aussi, il est possible que vous l'ayez déjà lu auparavant.

---

### Sommaire
* [Introduction](#introduction)
* [Un singleton, c'est quoi ?](#singleton)
* [Implémentation naïve](#implementation_naive)
* [Implémentation avec CRTP](#implementation_crtp)

---


```cpp
#include <iostream>
#include <vector>

template<class T>
class Singleton{
public:
  static T& instance(){
    static T inst{};
    return inst;
  }
protected:
  Singleton() = default;
  Singleton(Singleton const&) = delete;
  Singleton(Singleton&&) = delete;
  Singleton& operator=(Singleton const&) = delete;
  Singleton& operator=(Singleton&&) = delete;
  virtual ~Singleton() = default;
};

class Foo final : public Singleton<Foo>{
    friend class Singleton<Foo>;
private:
  // ...

  Foo() = default;
};

int main(){
  const auto& f = Foo::instance();
  (void)f;
}
```
