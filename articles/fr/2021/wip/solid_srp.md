## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) SOLID 1/5 - Le SRP

---

### Introduction

Plusieurs mois se sont écoulés depuis que j'ai annoncé travailler sur une synthèse des principes SOLID.
En effet, en plus d'être toujours ponctuel (hein, comment ça je mens mal ?), j'essaie avant tout de tenir parole.

Mais récapitulons : SOLID, ça veut dire quoi ? Il s'agit d'un acronyme désignant... d'autres acronymes.
Bon, pour être plus précis, il s'agit de cinq grands principes à respecter en programmation, si l'on veut avoir un code qui soit un minimum lisible, maintenable et évolutif.
Ils ne garantissent à eux seuls absolument pas une architecture exemplaire, mais offrent de bonnes bases pour concevoir quelque chose de viable.

Ces fameux principes sont les suivants :
* SRP (**S**ingle **R**esponsibility **P**rinciple) ;
* OCP (**O**pen/**C**losed **P**rinciple) ;
* LSP (**L**iskov **S**ubstitution **P**rinciple) ;
* ISP (**I**nterface **S**egregation **P**rinciple) ;
* DIP (**D**ependency **I**nversion **P**rinciple).

Soit, dans la langue de Kev Adams :
* Principe de responsabilité unique ;
* Principe ouvert/fermé ;
* Principe de substitution de Liskov ;
* Principe de ségrégation des interfaces ;
* Principe d'investion de dépendances.

Au cours de cet article, nous présenterons le premier de ces principes, à savoir le principe de responsabilité unique.
Pour les différents exemples, j'emploierai le langage C#.

---

### La théorie

En théorie, le SRP pourrait être énoncé ainsi :
> Toute entité *-qu'il s'agisse d'une classe, d'une fonction, ...-* doit avoir **une et une seule** responsabilité. Elle doit rendre **un** service, et **bien** le rendre.

Beaucoup de définitions s'arrêtent à l'application à la POO, mais ce serait un peu réducteur ; le SRP peut s'appliquer à d'autres paradigmes.

Les intérêts du SRP sont multiples :
* Réduction de la complexité du code : si la responsabilité d'une entité est clairement définie, sa lecture la sera tout autant ;
* Meilleure réutilisation d'une même entité : on n'embarque que ce dont on a réellement besoin, sans fonctionnalités non désirées ;
* Meilleure testabilité : on peut se concentrer à tester les différents cas d'*une* fonctionnalité ;
* Moins de dépendances : une entité ne dépendra que du strict nécessaire.

--- 

### Application

Pour illustrer un non-respect du SRP, voici un exemple de **mauvais** code :
```cs
// ATTENTION : Cet exemple est réalisé par un professionnel.
// Ne tentez pas de le reproduire chez vous, et laissez-le hors de portée des enfants.

class Serializer { // Le nom n'en dit pas assez !
    JsonContent ToJson<T>(T value) {     // 1ère responsabilité
        // ...
    }
    
    T FromJson<T>(JsonContent content) { // 2ème responsabilité
        // ...
    }
    
    XmlContent ToXml<T>(T value) {       // 3ème responsabilité 
        // ...
    }
    
    T FromXml<T>(XmlContent content) {   // 4ème responsabilité !?
        // ...
    }
}
```
Dans cet exemple, `Serializer` endosse au moins trois responsabilités de trop.
En effet, tel qu'il est écrit ici, il peut :
* Transformer un objet en JSON ;
* Transformer du JSON en un objet ;
* Transformer un objet en XML ;
* Transformer du XML en un objet.

De plus, l'aspect générique n'arrange vraiment pas les choses, car il induit qu'une seule instance peut (dé)sérialiser plusieurs objets de plusieurs types différents !

Ajoutons à cela que son nom n'est pas suffisamment évocateur et nous sommes sûrs de ne pas respecter le SRP.

> Mais dis donc Jamy ! Est-ce que c'est pas plus pratique comme ça ?

Avoir un seul objet pour faire plein de trucs, c'est super pratique !
Enfin, en apparence, car pour la maintenance, les tests, la relecture... Les choses se compliquent.

Pour un cas aussi simple, la solution est évidente : il suffit de créer quatre entités distinctes.
```cs
class JsonSerializer<T> {
    JsonContent ToJson(T value) {
        // ...
    }
}

class JsonDeserializer<T> {
    T FromJson(JsonContent content) {
        // ...
    }
}

// Etc
```
Dans l'idéal, il peut même faire sens d'avoir une fonction libre (c'est à dire qui n'appartient pas à une classe) afin d'alléger le code appelant d'une instanciation superflue.
Tous les langages ne le permettent pas, bien que ce soit possible de tricher, même de manière peu élégante (qui a dit `static class` ?).

---

### En résumé
* Eviter les classes type `XXXManager`
* Penser service
* Définir clairement **la** responsabilité d'une entité
