## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Présentation du design pattern NVI

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
Beaucoup de langages de programmation supportent la programmation dite "orientée objet". A cet effet, il est possible de créer des classes, qui serviront ensuite à la création d'instances. Il existe également la notion d'héritage, où une classe `Child` peut dériver d'une classe `Parent`.

Ce mécanisme permet à `Child` d'hériter des propriétés (attributs et méthodes) de `Parent`.

Exemple en C# :
```cs
class Parent {
  public Parent() {
    // ...
  }
  
  public void Foo() {
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
Une interface peut être déclarée de manière comparable à une classe, excepté que nous devons utiliser le mot-clé `interface` et non `class`/`struct`, et en suivant certaines règles :
* Les méthodes ne peuvent pas avoir de corps ;
* Il n'est pas possible de déclarer des attributs ;
* Tout est par défaut public, et cette visibilité est immuable ;
* Toute classe dérivant (implémentant) une interface doit implémenter ses méthodes.

Exemple :
```cs
interface IFileReader {
  #region Properties
  string Content { get; }
  bool IsOpen { get; }
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
En **programmation par contrat**, il existe plusieurs notions, telles que les **invariants**, les **préconditions**, et les **postconditions**. Afin d'aborder la suite de cet article de la meilleure manière possible, présentons brièvement ces différents points.

Un **invariant** est une condition qui peut être vérifiée à tout instant *t*. Nous ne nous intéresserons ici qu'aux invariants de classe. Un invariant de classe est une propriété qui doit être vraie pour tout objet à tout instant après sa construction. Par exemple, pour un objet de type `Rectangle`, la longueur et la largeur doivent toutes deux être positives. Pour un objet de type `Date`, le jour doit être compris entre 1 et 31 (variable selon les mois), et le mois doit être compris entre 1 et 12.

Une **précondition** est une condition qui doit être vérifiée au début d'une opération donnée. Par exemple, dans le cas d'une fonction `division`, le dénominateur doit être différent de zéro. Cette condition primordiale au bon déroulement de l'opération est une précondition. 

Enfin, une **postcondition** est une condition qui doit être vérifiée à la fin d'une opération donnée. Les postconditions aident bien souvent à détecter des erreurs de programmation (postcondition non vérifiée == erreur dans l'opération), aussi ai-je l'habitude de les vérifier avec des assertions, par opposition aux préconditions, que je traite avec des exceptions.

**Note** - *Ces notions sont primordiales à assimiler afin d'appréhender la suite de cet article.*

En programmation orientée objet, une interface n'est en réalité rien de plus qu'un contrat. Il s'agit d'un contrat passé avec une classe, qui induit le fait suivant : *toute classe implémentant cette interface devra implémenter toutes ses méthodes*. Soit "*si j'instancie un objet de type `T1`, où `T1` implémente l'interface `I1`, je dois pouvoir appeler la méthode `M1` de `T1` héritée de `I1`*". Voilà pourquoi tout est public dans une interface.

De plus, conformément au LSP (**L**iskov **S**ubstitution **P**rinciple), pour tout objet `a` de type `T1`, où `T1` hérite de `T2`, les préconditions, postconditions et invariants de toute instance de `T2` doivent être vérifiés pour `a`.

---

#### <a name="nvi">NVI - Introduction et exemples</a>
Le design pattern NVI (**N**on **V**irtual **I**nterface) permet de vérifier la validité d'invariants, de préconditions et de postconditions automatiquement de manière à ce que l'utilisateur de nos entités puisse se passer de les écrire. La notion d'interface "non virtuelle" peut en dérouter certains (à juste titre !), laissez-moi donc vous présenter l'idée résidant derrière ce terme farfelu.

Pour ce faire, il convient d'utiliser des classes dites abstraites, c'est-à-dire dont on ne peut pas créer d'instances. Ces classes seront à la base de nos relations d'héritages (en lieu et place des interfaces), et effectueront les vérifications relatives aux invariants, préconditions et postconditions.

Reprenons l'exemple de l'interface `IFileReader` :
```cs
interface IFileReader {
  #region Properties
  string Content { get; }
  bool IsOpen { get; }
  #endregion
  
  #region Methods
  void Open(string filename);
  void Close(); 
  #endregion
}
```
Cette interface induit intrinsèquement certaines vérifications triviales à effectuer :
* A l'appel de `Open`, le chemin fourni doit être valide ;
* A l'appel de `Close`, le fichier doit être ouvert ;
* Lorsque l'on accède à `Content`, le fichier doit être ouvert.

Si une classe `XmlFileReader` implémente `IFileReader`, elle devra faire ces vérifications. Elle pourra ensuite vérifier le formattage du fichier afin de s'assurer qu'il s'agit bien d'un fichier XML. Puis, si une classe `JsonFileReader` implémente à son tour `IFileReader`, il faudra à nouveau faire ces vérifications. Il en va de même pour une classe `IniFileReader` ou tout autre format.

Le design pattern NVI résout tous ces problèmes en centralisant les vérifications.
Voici l'équivalent de `IFileReader` en respectant le DP NVI :
```cs
// Cette classe a le même rôle que 'IFileReader'
abstract class BaseFileReader {
  #region Public properties
  public string Content { get { return GetContent(); } }
  public bool IsOpen { get; private set; } = false;
  #endregion

  #region Private fields
  private string content = null;
  #endregion

  #region Public methods
  // Effectue les vérifications relatives à 'filename', puis appelle 'Open_Impl'
  public void Open(string filename) {
    Debug.Assert(!string.IsNullOrEmpty(filename), "Invalid file name");
    Debug.Assert(File.Exists(filename), "No such file {filename}");
    if (this.IsOpen) {
      this.Close();
    }
    this.Open_Impl(filename);
    this.IsOpen = true;
  }

  // Effectue les vérifications relatives à l'état interne de l'instance courante,
  // puis appelle 'Close_Impl'
  public void Close() {
    Debug.Assert(this.IsOpen, "No file open");
    this.Close_Impl();
    this.IsOpen = false;
  }
  #endregion

  #region Protected methods
  // Pourra être appelé dans 'Open_Impl'
  protected void SetContent(string content) {
    this.content = content;
  }
  #endregion

  #region Private methods
  // Effectue les relatives à l'état interne de l'instance courante, puis renvoie 'content'
  private string GetContent() {
    Debug.Assert(this.IsOpen, "No file open");
    return this.content;
  }
  #endregion

  #region Unimplemented methods
  protected abstract void Open_Impl(string filename);
  protected abstract void Close_Impl();
  #endregion
}
```

**Note** - *Je ne suis habituellement pas partisan des getters et setters, ces derniers ayant comme défaut principal de briser l'encapsulation. Dans le cas présent, la visibilité de `GetContent` et `SetContent` ne permettent pas à une entité tierce de modifier l'état d'une instance d'une classe héritant de `BaseFileReader`. Ainsi, leur utilisation dans le cas présent est valide.*

Seules les méthodes `Open_Impl` et `Close_Impl` devront être implémentées ; ainsi, la logique des classes filles pourra se passer des vérifications tierces relatives à leur bon fonctionnement. Ce qui induit qu'une classe `XmlFileReader` pourrait être implémentée ainsi :

```cs
class XmlFileReader : BaseFileReader {
  public XmlFileReader(string filename) : base() {
    this.Open(filename);
  }

  protected override void Open_Impl(string filename) {
    // Lecture du fichier
    // Eventuelles vérifications sur le contenu du fichier
  }

  protected override void Close_Impl() {
    // Fermeture du fichier
  }
}
```

Et la logique est la même pour toute autre classe implémentant `BaseFileReader` !
En somme, tout invariant d'une classe abstraite est vérifié sans besoin d'adapter la classe concrète qui en dérive. Il en va de même pour les préconditions et les postconditions.
