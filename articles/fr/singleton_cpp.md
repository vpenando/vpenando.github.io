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
  Singleton() = default;
  ~Singleton() = default;
  // Deleted members
  Singleton(Singleton const&) /*      */ = delete;
  Singleton(Singleton&&)      /*      */ = delete;
  Singleton& operator=(Singleton const&) = delete;
  Singleton& operator=(Singleton&&) /**/ = delete;
};

// ...

// A l'usage :
auto& singleton = Singleton::instance();
singleton.foo();
singleton.bar();
```

---

#### <a name="implementation_crtp">Implémentation avec CRTP</a>

Pour paraphraser Wikipédia :
> The curiously recurring template pattern (CRTP) is an idiom in C++ in which a class X derives from a class template instantiation using X itself as template argument.

Soit :
```cpp
template<class Derived>
class CRTP_Base
{
};

class CRTP_Derived : public CRTP_Base<CRTP_Derived>
{
};
```

```cpp
template<class T>
class Singleton {
public:
  static T& instance() {
    static T inst{};
    return inst;
  }
protected:
  // Defaulted members
  /*   */  Singleton() = default;
  virtual ~Singleton() = default;
private:
  // Deleted members
  Singleton(Singleton const&) /*      */ = delete;
  Singleton(Singleton&&)      /*      */ = delete;
  Singleton& operator=(Singleton const&) = delete;
  Singleton& operator=(Singleton&&) /**/ = delete;
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
