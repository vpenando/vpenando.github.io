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

#### <a name="singleton">Introduction</a>

---

#### <a name="singleton">Un singleton, c'est quoi ?</a>

---

#### <a name="implementation_naive">Implémentation naïve</a>
Une implémentation simpliste d'un singleton pourrait ressembler à ceci :
```cpp
class Singleton {
public:
    static Singleton& instance() {
        static Singleton inst{};
        return inst;
    }
  
    void foo() { /* ... */ }
    void bar() { /* ... */ }
    
private:
    Singleton()  = default;
    ~Singleton() = default;
    Singleton(Singleton const&) = delete;
    Singleton(Singleton&&) /**/ = delete;
    Singleton& operator=(Singleton const&) = delete;
    Singleton& operator=(Singleton&&) /**/ = delete;
};

// ...

// A l'usage :
auto& instance = Singleton::instance();
instance.foo();
instance.bar();
```
Cet exemple est tout à fait fonctionnel. Néanmoins, si l'on doit réaliser un autre type de singleton en plus de celui-ci, il faudrait alors réécrire une classe très similaire.
Or, pour éviter toute duplication de code, il est possible (et conseillé !) d'imaginer une classe qui embarque toutes les propriétés d'un singleton et prendrait un paramètre template approprié ! Ainsi, cela permettrait de centraliser le code concerné :

```cpp
template<class T>
class Singleton {
public:
    static T& instance() {
        static T inst{};
        return inst;
    }
  
protected:
    Singleton()  = default;
    ~Singleton() = default;

private:
    Singleton(Singleton const&) = delete;
    Singleton(Singleton&&) /**/ = delete;
    Singleton& operator=(Singleton const&) = delete;
    Singleton& operator=(Singleton&&) /**/ = delete;
};

class Foo final {
public:
    Foo()  = default;
    ~Foo() = default;
  
    void foo() { /* ... */ }
    void bar() { /* ... */ }
};

using SingleFoo = Singleton<Foo>;
```
Toutefois, cet exemple, bien que viable, pose le problème de la sémantique de la classe `Foo`. En effet, le fait de pouvoir à tout moment créer une instance de cette classe contredit le principe même du singleton :
```cpp
// Ce code est valide :
Foo& single_foo = SingleFoo::instance();
Foo foo{}; // Quel intérêt d'avoir un singleton si l'on peut faire ça ?
```

Une solution à ce problème permet d'utiliser conjointement une classe `Singleton` très similaire à l'exemple ci-dessus et l'héritage. Cette solution est appelée CRTP.

---

#### <a name="implementation_crtp">Implémentation avec CRTP</a>

Pour paraphraser Wikipédia :
> The curiously recurring template pattern (CRTP) is an idiom in C++ in which a class X derives from a class template instantiation using X itself as template argument.

Soit :
```cpp
template<class Derived>
class CRTP_Base {
};

class CRTP_Derived: public CRTP_Base<CRTP_Derived> {
};
```
Donc, dans le cas d'un singleton :
```cpp
template<class T>
class Singleton {
public:
    static T& instance() {
        static T inst{};
        return inst;
    }

protected:
    /*   */  Singleton() = default;
    virtual ~Singleton() = default;

private:
    // Deleted members
    Singleton(Singleton const&) = delete;
    Singleton(Singleton&&) /**/ = delete;
    Singleton& operator=(Singleton const&) = delete;
    Singleton& operator=(Singleton&&) /**/ = delete;
};

class Foo final: public Singleton<Foo> {
    friend class Singleton<Foo>;
private:
    // ...

    Foo() = default;
};

int main() {
    const auto& foo = Foo::instance();
    (void)foo;
}
```
