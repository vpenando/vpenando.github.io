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

---

#### <a name="poo_interfaces">POO & Interfaces</a>
Une interface peut être déclarée de manière comparable à une classe, excepté que nous devons utiliser le mot-clé `interface` et non `class` (ou `struct` en fonction des cas), et en suivant certaines règles :
* Les méthodes ne peuvent pas avoir de corps ;
* Il n'est pas possible de déclarer des attributs ;
* Tout est par défaut public, et cette visibilité est immuable ;
* Toute classe dérivant (implémentant) une interface doit implémenter ses méthodes.

Exemple :
```cs
interface IFileReader {
  #region Properties
  string Content { get; protected set; }
  bool IsOpen { get; private set; }
  #endregion
  
  #region Methods
  void Open(string filename);
  void Close(); 
  #endregion
}
```

Ainsi, toute classe implémentant l'interface `IFileReader` se doit d'implémenter ses méthodes.
**Note** - *Ces méthodes seront par défaut publiques. Nous allons expliquer pourquoi dans la partie suivante.*

Il sera alors possible d'avoir un code similaire à :
```cs
class FileReader : IFileReader {
  #region Public properties
  public string Content { get; protected set; } = null;
  public bool IsOpen { get; private set; } = false;
  #endregion

  #region Private fields
  private string content = null;
  #endregion

  #region Public methods
  public void Open(string filename) {
    // ...
  }

  public void Close() {
    // ...
  }
  #endregion
}
```
Et, à un autre endroit du code :

```cs
void Foo(IFileReader reader) {
  // ...
}

// ...

var reader = new FileReader();
Foo(reader);
```

---

#### Contrats
En **programmation par contrat**, il existe plusieurs notions, telles que les **invariants**, les **préconditions**, et les **postconditions**. Afin d'aborder la suite de cet article de la meilleure manière possivle, présentons brièvement ces différents points.

Un **invariant** est une condition qui peut être vérifiée à tout instant *t*. Nous ne nous intéresserons ici qu'aux invariant de classe. Un invariant de classe est une propriété qui doit être vraie à tout instant. Par exemple, pour un objet de type `Rectangle`, la longueur et la largeur doivent toutes deux être positives. Pour un objet de type `Date`, le jour doit être compris entre 1 et 31 (variable selon les mois), et le mois doit être compris entre 1 et 12.

Une **précondition** est une condition qui doit être vérifiée au début d'une opération donnée. Par exemple, dans le cas d'une fonction `division`, le dénominateur doit être différent de zéro. Cette condition primordiale au bon déroulement de l'opération est une précondition. 

Enfin, une **postcondition** est une condition qui doit être vérifiée à la fin d'une opération donnée. Les postconditions aident bien souvent à détecter des erreurs de programmation (postcondition non vérifiée == erreur dans l'opération), aussi ai-je l'habitude de les vérifier avec des assertions, par opposition aux préconditions, que je traite avec des exceptions.

Todo:
Invariants

Préconditions

Postconditions

LSP

#### <a name="nvi">NVI - Introduction et exemples</a>

