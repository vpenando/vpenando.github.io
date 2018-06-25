## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Du manager à l'entropie

---

#### Introduction
En programmation orientée objet, on est souvent tenté de créer des "managers", ces fameuses classes qui "gèrent des choses".
Par exemple, une classe devant "gérer" un fichier texte pourrait être exprimée ainsi :
```cs
public class FileManager {
  public FileManager(string path) {
    // ...
  }
  
  public void Create() {
    // ...
  }
  
  public void Write(string content) {
    // ...
  }
  
  public string Read() {
    // ...
  }
  
  public bool Exists() {
    // ...
  }
}
```

Ou, pire encore :
```cs
public class FileManager {
  public FileManager() {
    // ...
  }
  
  public void Create(string path) {
    // ...
  }
  
  public void Write(string path, string content) {
    // ...
  }
  
  public string Read(string path) {
    // ...
  }
  
  public bool Exists(string path) {
    // ...
  }
}
```
***Note** - La seconde version est encore pire car elle respecte encore moins le SRP du fait qu'une instance de cette classe est susceptible de "gérer" plusieurs fichiers.*

Nous sommes en droit de nous poser la question suivantes : *En quoi est-ce si mal de créer un "manager" ? Tant que mon entité doit gérer des choses, il semble tout à fait légitime, voire approprié, de l'appeler `XXXManager`, non ?*

---

#### Explication
En effet, une classe devant "gérer des choses" peut tout à fait être appelée "manager". Le problème ne se situe pas en ce point, mais plutôt en l'essence même de la classe. L'existence d'une telle entité mène, à terme, à une architecture bancale qui accélère un phénomène que l'on appelle l'[**entropie du logiciel**](https://gist.github.com/sroccaserra/6cafd444c11958059fdd2a698d4effcb).

Avant tout, présentons *en quoi* créer un `XXXManager` est une mauvaise idée. En POO, il convient de respecter a minima les [principes SOLID](https://en.wikipedia.org/wiki/SOLID). Dans notre cas, nous allons surtout nous intéresser au premier d'entre eux, à savoir le [SRP](https://en.wikipedia.org/wiki/Single_responsibility_principle), qui énonce la chose suivante :
> Toute fonction, classe, ou module, doit avoir une et une seule responsabilité. Cette entité doit rendre **un** service, et **bien** le rendre.

Autant donc dire que nous sommes bien mal partis avec une classe qui endosse -pour le moment-, plusieurs responsabilités :
* Créer un fichier ;
* Ecrire dans un fichier ;
* Lire le contenu d'un fichier ;
* Tester le contenu d'un fichier.

Pour le moment, les fonctions de ce `FileManager` sont somme toutes assez triviales. Soyons fous, ajoutons-en quelques unes.
Le jour où l'on veut pouvoir supprimer un fichier, il suffira d'ajouter une méthode `Delete`, n'est-il pas ? Et si l'on décide soudainement de pouvoir "gérer" des fichiers XML, par exemple en testant leur format, que va-t-on faire ? Ajouter une fonction `IsXmlFile` ? Ou pire encore, créer un `XmlFileManager`, qui hérite de `FileManager` ?
Nous nous retrouvons alors avec des classes [qui ont de nombreuses responsabilités, et qu'il va être très difficile de maintenir](https://openclassrooms.com/forum/sujet/fonction-find-de-std-map-sur-std-function?page=1#message-92459036).

C'est à ce 
