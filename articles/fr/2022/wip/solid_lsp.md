## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) SOLID 3/5 - Le LSP

---

### Introduction

Le **LSP** (**L**iskov **S**ubstitution **P**rinciple, ou principe de substitution de Liskov) est l'un des 5 principes **SOLID**.

Il peut être énoncé comme suit :

> Tout invariant inhérent à un type `T` doit être valide pour un type `U` si `U` veut se substituer à `T`. De plus, les préconditions propres à `T` ne peuvent pas être renforcées dans `U` et les postconditions de `T` ne peuvent pas être affaiblies dans `U`.

Pour faire simple, si une classe `Child` hérite d'une classe `Parent`, alors toute instance de `Child` peut être utilisée en tant que `Parent`, de la même manière et sans altérer son comportement.

Notons par ailleurs que cela ne concerne pas que la POO, mais est également applicable à la programmation générique.

---

### Exemple

Voici un exemple de non-respect du LSP, volontairement simpliste, ici en C#.
```c#
// Un simple rectangle
record Rectangle(float Width, float Height);
```
Spécialisons notre classe :
```c#
// Un carré n'est qu'un rectangle un peu particulier, n'est-ce pas ?
record Square(float Side) : Rectangle(Side, Side);
```

Avant toute chose, il ne fait aucun sens qu'un carré possède une largeur et une hauteur ; en effet, tous ses côtés doivent avoir la même taille (précondition renforcée !).

Puis, imaginons que l'on enrichisse un peu notre type `Rectangle` :
```c#
// Toujours un simple rectangle
record Rectangle(float Width, float Height) {
    public Rectangle Resize(float w, float h)
        => this with { Width = w, Height = h };
}
```
Ensuite, lors de l'appel à `Resize`, si l'appelant est de type `Square`, que se passe-t-il ?
```c#
Square square = new(10, 10);
Square other = square.Resize(4, 2); // Un carré de 4 par 2 !?
```
On casse alors l'essence même de notre carré !

Il est toutefois parfaitement sensé de pouvoir appeler `Resize` sur un rectangle ; ce faisant, il devient alors limpide que nos deux classes `Rectangle` et `Square` ne doivent pas avoir le moindre lien de parenté, car une méthode s'appliquant pour l'un de nos types doit pouvoir s'appliquer à l'autre.

---

### Solutions possibles

D'une manière générale, une classe à sémantique de valeur ne doit pas être héritée. En C#, il est courant de la marquer comme `sealed` ou d'utiliser une `struct`, qui est implicitement non-héritable (`record struct` si l'on veut utiliser un record).
En l'occurrence, notre type `Rectangle` n'étant qu'un simple aggrégat sans notion d'identité, il correspond tout à fait et pourrait donc être déclaré ainsi :
```c#
record struct Rectangle(float Width, float Height) {
    public Rectangle Resize(float w, float h)
        => this with { Width = w, Height = h };
}
```

- `struct` (pour value objects, non héritables)
- `sealed class`
