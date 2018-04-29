## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### [FR] Présentation du design pattern NVI

###### Note importante
> Cet article provient de mon "ancien" dev blog, avec quelques petits remaniements. Aussi, il est possible que vous l'ayez déjà lu auparavant.

---

### Sommaire
* [Introduction](#introduction)
* [POO & Interfaces](#poo_interfaces)
* [Contrats](#contrats)
* [NVI - Introduction et exemples](#nvi)

---

#### Introduction
Dans beaucoup de langages de programmation, il est possible de créer des classes, qui serviront ensuite à la création d'instances. Il existe également une notion de hiérarchie, où une classe `Child` peut dériver d'une classe `Parent`.
Ce mécanisme permet à `Child` d'hériter de certaines propriétés de `Parent` en fonction de leur visibilité.

Exemple en C# :
```cs
class Parent {
  public Parent() {
    // ...
  }
  
  void Foo() {
    // ...
  }
}

class Child : Parent {
  public Child() : base() {
    // ...
  }
  
  // Child hérite naturellement de 'Foo'
}
```
Parallèlement, et dans une logique assez similaire, il existe la notion d'interface, que nous allons présenter dans la partie suivante.

#### <a name="poo_interfaces">POO & Interfaces</a>



#### Contrats

#### <a name="nvi">NVI - Introduction et exemples</a>

