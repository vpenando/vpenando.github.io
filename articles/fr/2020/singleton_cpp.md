## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Réflexion - Implémentation d'un singleton en C++

###### Note importante
> Cet article provient de mon "ancien" dev blog, avec quelques petits remaniements. Aussi, il est possible que vous l'ayez déjà lu auparavant.

---

### Sommaire
* [Introduction - Un singleton, c'est quoi ?](#singleton)
* [Implémentation naïve](#implementation_naive)
* [Implémentation avec CRTP](#implementation_crtp)
* [Conclusion](#conclusion)

---

#### <a name="singleton">Introduction - Un singleton, c'est quoi ?</a>
Un singleton est une classe dont il n'existe qu'une seule instance, et qui expose une méthode ou propriété publique et statique afin d'y accéder. Il s'agit donc d'une forme de variable globale, mais version POO.

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
template<class T>
class Base {
public:
    void foo();
    void bar();
};

class Derived: public Base<Derived> {
};
```
Dans le cas d'un singleton, l'application ressemblerait à ceci :
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
    Singleton(Singleton const&) = delete;
    Singleton(Singleton&&) /**/ = delete;
    Singleton& operator=(Singleton const&) = delete;
    Singleton& operator=(Singleton&&) /**/ = delete;
};

class Foo final: public Singleton<Foo> {
    friend class Singleton<Foo>;  // Notez la relation d'amitié
                                  // afin de permettre l'appel à Foo::Foo() depuis Singleton
public:
    void foo();
    void bar();
    
    // Héritée :
    //static Foo instance();
    
private:
    Foo() = default;
};
```
Ainsi, la classe `Foo` :
* Expose l'interface publique de `Singleton` ;
* Ne peut pas être instanciée n'importe où (grâce à son constructeur privé) ;
* Ne permet pas de construction par copie ou déplacement (via les constructeurs supprimés de `Singleton`).

Nous avons donc ici un singleton qui expose les bonnes restrictions afin de remplir au mieux son rôle.

---

#### <a name="conclusion">Conclusion</a>
Pour résumer, le CRTP, bien que déroutant au premier abord, est probablement la manière la plus efficace pour implémenter un singleton. En effet, il permet d'éviter de dupliquer inutilement du code et d'ajouter simplement l'interface d'un singleton à une classe.
