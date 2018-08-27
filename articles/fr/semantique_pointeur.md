## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) De la sémantique du pointeur

---

### Introduction
En C et en C++ (bien que je n'aborderai dans cet article que le C++), nous avons la possibilité d'allouer manuellement de la mémoire pour des usages spécifiques (collection hétérogène par exemple) :
```cpp
class Foo { /* ... */ };
class Bar : public Foo { /* ... */ };

// ...

std::vector<Foo *> vec;
vec.push_back(new Foo{/* ... */});
vec.push_back(new Bar{/* ... */});
```
***Note*** - *J'exclus volontairement l'usage des pointeurs en tant que tableaux, car nous avons `std::vector`, `std::array` et `std::dynarray` pour cet usage. L'utilisation de pointeurs en tant que tableaux est généralement à bannir.*

Du fait d'un phénomène appelé *slicing*, il convient de passer par des pointeurs ou des références lorsque nous passons des objets comme arguments à des fonctions :
```cpp
class Base {
public:
  /*   */  Base() = default;
  virtual ~Base() = default;
  virtual void foo() { std::cout << "Base::foo"; }
};

class Derived final : public Base {
public:
  Derived() = default;
  virtual void foo() override { std::cout << "Derived::foo"; }
};

// Passage par copie => slicing
void foo_by_value(Base obj) {
  obj.foo();
}

int main() {
  Derived d{};
  foo_by_value(d); // 'd' sera "tronqué"
}
```
Car contre toute attente, l'output de ce programme ne sera pas `Derived::foo` mais bien `Base::foo`. En effet, à l'appel de `foo_by_value`, l'objet passé sera "tronqué" de manière à ce que seule la partie provenant de `Base` soit gardée. En passant par un pointeur ou une référence, l'objet sera conservé "entier".

Ainsi donc, lorsque vous devez passer des objets d'un type pouvant être dérivé, il convient de passer par l'une de ces solutions.

---

### Pointeur VS référence
Se pose à présent la question du choix entre pointeur et référence : lequel utiliser dans quel(s) cas ?

A mon sens, il existe trois cas dans lesquels utiliser des pointeurs :
* Collection hétérogène
* Modification d'une ressource propriétaire (1)
* Utilisation de fonction C

*(1) Ce que j'entends par "propriétaire", c'est qu'il appartient à l'appelant d'allouer et de libérer la mémoire. Bien souvent, nous utilisons des pointeurs intelligents, ce qui induit que la question n'aura pas à se poser.*

Dans les autres cas, nous utiliserons plutôt des références (de préférence constantes).
Cependant, pourquoi utiliser un pointeur plutôt qu'une référence dans le cas de la modification d'une ressource ? La réponse est relativement simple :
```cpp
void foo(int& i) {
  // Modification de 'i'
}

// ...
int i = 42;
foo(i);
```
Dans cet exemple, la simple lecture de `foo(i)` ne permet pas de deviner que l'on passe une variable par référence ; de ce fait, le relecteur ne saura pas systématiquement que `foo` produit un effet de bord.
En revanche `foo(&ptr)` apporte cette sémantique, évitant cet oubli.

---

### Pointeur & ownership
Il y a cependant des cas où il incombe à la fonction appelée de libérer la mémoire. Ainsi, il y a des cas où le passage de pointeur comme paramètre induit que la responsabilité de la libération de l'objet appartient à la fonction appelée.

Pour lever cette ambiguïté, un simple alias suffit :
```cpp
template<class T>
using Owner = T;
```
