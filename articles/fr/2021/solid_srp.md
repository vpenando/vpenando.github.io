## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) SOLID 1/5 - Le SRP

---

### Introduction

Plusieurs mois se sont écoulés depuis que j'ai annoncé travailler sur une synthèse des principes SOLID.
En effet, en plus d'être toujours ponctuel (hein, comment ça je mens mal ?), j'essaie avant tout de tenir parole.

Mais récapitulons : SOLID, ça veut dire quoi ? Eh bien, il s'agit en fait d'un acronyme désignant... d'autres acronymes.
Bon, pour être plus précis, il s'agit de cinq grands principes à respecter en programmation, si l'on veut avoir un code qui soit un minimum lisible, maintenable et évolutif.
Ils ne garantissent à eux seuls absolument pas une architecture exemplaire, mais offrent de bonnes bases pour concevoir quelque chose de viable.

Ces fameux principes sont les suivants :
* Le SRP (**S**ingle **R**esponsibility **P**rinciple) ;
* L'OCP (**O**pen/**C**losed **P**rinciple) ;
* Le LSP (**L**iskov **S**ubstitution **P**rinciple) ;
* L'ISP (**I**nterface **S**egregation **P**rinciple) ;
* Le DIP (**D**ependency **I**nversion **P**rinciple).

Soit, dans la langue de Kev Adams :
* Le principe de responsabilité unique ;
* Le principe ouvert/fermé ;
* Le principe de substitution de Liskov ;
* Le principe de ségrégation des interfaces ;
* Le principe d'inversion de dépendances.

Au cours de cet article, nous présenterons le premier de ces principes, à savoir le principe de responsabilité unique.
Pour les différents exemples, j'emploierai le langage C#.

---

### La théorie

En théorie, le SRP pourrait être énoncé ainsi :
> Toute entité (classe ou fonction/méthode) doit avoir **une et une seule** responsabilité, c'est à dire qu'elle doit rendre **un** service, et **bien** le rendre.

Beaucoup de définitions s'arrêtent à tort à l'application à la POO, mais ce serait un peu réducteur ; le SRP est notamment applicable à des fonctions, et peut donc s'adapter à d'autres paradigmes.
Une exception au SRP est le cas particulier des *namespaces* et des modules, qui peuvent simplement être vus comme des espaces de rangement.
Ils n'ont aucune sémantique autre que l'organisation du code. À titre d'exemple, il fait tout à fait sens de ranger des classes `List` et `Array` dans un module ou namespace `Collections`.

Les intérêts du SRP sont multiples :
* Réduction de la complexité du code : si la responsabilité d'une entité est clairement définie, sa lecture la sera tout autant ;
* Meilleure réutilisation d'une même entité : on n'embarque que ce dont on a réellement besoin, sans fonctionnalités non désirées ;
* Meilleure testabilité : on peut se concentrer à tester les différents cas d'*une* fonctionnalité à la fois ;
* Moins de dépendances : une entité ne dépendra que du strict nécessaire.

En somme, cela permet d'éviter de complexifier le code à tel point qu'il devienne pratiquement impossible à maintenir.
À titre personnel, j'ai eu à travailler sur un projet de ce type par le passé, et modifier ou ajouter une fonctionnalité était si lourd qu'il fallait effectuer un monstrueux travail de refactoring en amont. La plupart des fonctions faisait entre 200 et 1000 lignes, et il fallait parfois les scinder en de nombreuses sous-fonctions pour n'extraire que ce dont on avait besoin ; cela ajoutait une tâche colossale quasiment à *chaque fois* que l'on devait toucher à ce projet.

--- 

### Application

Pour illustrer un non-respect du SRP, voici un exemple de **mauvais** code :
```cs
// ATTENTION : Cet exemple est réalisé par un professionnel.
// Ne tentez pas de le reproduire chez vous, et laissez-le hors de portée des enfants.

// Un indice : le nom n'en dit pas assez !
class Serializer {

    // 1ère responsabilité
    public JsonContent ToJson<T>(T value) {
        // ...
    }
    
    // 2ème responsabilité
    public T FromJson<T>(JsonContent content) {
        // ...
    }
    
    // 3ème responsabilité 
    public XmlContent ToXml<T>(T value) {
        // ...
    }
    
    // 4ème responsabilité !?
    public T FromXml<T>(XmlContent content) {
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

En effet, à l'usage, c'est vraiment la fête !
```cs
// Toujours à laisser hors de portée des enfants.

var serializer = new Serializer();
var json = serializer.ToJson(maSuperInstance1);
var maSuperInstance2 = serializer.FromXml<MonSuperType2>(monSuperXml);
// Poussons le délire jusqu'au bout :
var jsonDepuisXml = serializer.ToJson(serializer.FromXml<MonSuperType2>(monSuperXml));
```

Ajoutons à cela que son nom n'est pas suffisamment évocateur et nous sommes sûrs de ne pas respecter le SRP.

> Mais dis donc Jamy ! Est-ce que c'est pas plus pratique comme ça ?

Avoir un seul objet pour faire plein de trucs, c'est super pratique !
Enfin, en apparence, car pour la maintenance, les tests, la relecture... Les choses se compliquent très rapidement.

Pour un cas aussi simple, la solution est évidente : il suffit de créer quatre entités distinctes.
```cs
class JsonSerializer<T> {
    public JsonContent ToJson(T value) {
        // ...
    }
}

class JsonDeserializer<T> {
    public T FromJson(JsonContent content) {
        // ...
    }
}

class XmlSerializer<T> {
    public XmlContent ToXml(T value) {
        // ...
    }
}

class XmlDeserializer<T> {
    public T FromXml(XmlContent content) {
        // ...
    }
}
```
Dans l'idéal, pour ce cas précis, avoir une **fonction libre** (c'est à dire qui n'appartient pas à une classe) pour chaque transformation est également **tout à fait approprié** afin d'alléger le code appelant d'une instanciation superflue.
Tous les langages ne le permettent pas, bien que ce soit possible de tricher, même de manière peu élégante (qui a dit `static class` ?).

Un autre exemple, plus probant, pourrait être une méthode (l'exemple étant en OO) retournant, disons, des individus majeurs depuis une base de données :
```cs
class UserRepository {
    private readonly DataBaseClient dbClient;
    
    public UserRepository() {
        this.dbClient = new DataBaseClient("localhost:1234", "user", "password");
    }
    
    public IEnumerable<User> GetMajorUsers() {
        return this.dbClient.Users
            .Where(u => u.Age >= 18)
            .ToList();
    }
    
    // ...
}
```
Cet exemple, aussi mince soit-il, endosse à lui seul **trois** responsbilités :
* Il instancie un `DataBaseClient` ;
* Il accède aux différents utilisateurs de la base de données ;
* Il détermine lui-même si un individu est majeur (sans parler de l'âge pouvant varier d'un pays à l'autre).

Un meilleur découpage pourrait être :
```cs
class UserRepository {
    private readonly DataBaseClient dbClient;
    
    public UserRepository(DataBaseClient client) {
        this.dbClient = client;
    }
    
    public IEnumerable<User> GetMajorUsers() {
        return this.dbClient.Users
            .Where(u => u.IsMajor)
            .ToList();
    }
    
    // ...
}
```

Notons toutefois qu'il ne faut absolument pas respecter aveuglément le SRP, et qu'il est même parfois parfaitement envisageable de volontairement aller à l'encontre.
Par exemple, pour le cas d'un serveur HTTP, il fait tout à fait sens d'avoir des logs afin de retracer l'historique d'un éventuel crash.
Ainsi, le serveur endossera une responsabilité implicite, mais cruciale.

Un autre exemple est la classe `List` en C# : cette classe endosse de multiples responsabilités (ajout / suppression d'éléments, tri, indexation, etc), mais il serait parallèlement inconfortable de ne pas les avoir, n'est-ce pas ?

***Note -** L'approche fonctionnelle, [telle que proposée par Elm](https://package.elm-lang.org/packages/elm/core/latest/List) proposant des fonctions libres (telles que `map`, `filter`, `sort`, `length`, etc) dans un module `List` reste toutefois la plus "SRP-compliant".*

---

### En résumé

Voici quelques astuces afin de tenter de respecter au mieux le SRP :
* Définir clairement **la** responsabilité d'une entité ;
* Eviter les classes telles que `XXXManager`, car un nom trop flou montre que notre entité est probablement mal découpée ;
* Bien réfléchir à l'interface publique de chaque type : certaines méthodes n'ont peut-être pas à être exposées !

J'insiste surtout sur les noms de classes se terminant par "Manager", "Handler" ou autre : généralement, on a tendance à appeler `MachinManager` une classe qui "fait plein de trucs".
Et c'est bien souvent (pour ne pas dire systématiquement) signe que l'on part dans une mauvaise direction.
De surcroît, il est sensé de penser que si vous avez du mal à nommer une entité, il est possible qu'elle soit simplement mal conçue, car ce qui se conçoit bien devrait théoriquement s'énoncer clairement.

Toutefois, comme dans l'exemple du serveur HTTP mentionné plus haut, il n'est pas insensé d'assumer que l'on déroge au SRP si cela a un réel intérêt.
