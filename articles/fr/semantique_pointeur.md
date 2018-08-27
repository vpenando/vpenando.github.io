## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) De la sémantique du pointeur

---

### Introduction
En C et en C++ (bien que je n'aborderai dans cet article que le C++), nous avons la possibilité d'allouer manuellement de la mémoire pour des usages spécifiques (collection hétérogène par exemple) :
```cpp
class Foo { /* ... */ };

// ...

std::vector<Foo *> vec;
vec.push_back(new Foo{/* ... */});
```
***Note*** - *J'exclus volontairement l'usage des pointeurs en tant que tableaux, car nous avons `std::vector`, `std::array` et `std::dynarray` pour cet usage. L'utilisation de pointeurs en tant que tableaux est généralement à bannir.*

Du fait d'un phénomène appelé *slicing*, il convient de passer par des pointeurs ou des références :
```cpp
class Base {
public:
  Base() = default;
  virtual ~Base() = default;
  virtual void foo() { std::cout << "Base::foo"; }
};

class Derived : public Base {
public:
  Derived() = default;
  virtual void foo() override { std::cout << "Derived::foo"; }
};

void foo_by_value(Base obj) {
  obj.foo();
}

int main() {
  Derived d{};
  foo_by_value(d);
}
```
Car contre toute attente, l'output de ce programme ne sera pas `Derived::foo` mais bien `Base::foo`. En effet, à l'appel de `foo_by_value`, l'objet passé sera "tronqué" de manière à ce que seule la partie provenant de `Base` soit gardée. En passant par un pointeur ou une référence, l'objet sera conservé "entier".

Ainsi donc, lorsque vous devez passer des objets d'un type pouvant être dérivé, il convient de passer par l'une de ces solutions.


### Pointeur VS référence


### Pointeur & ownership

```cpp
template<class T>
using Owner = T;
```
