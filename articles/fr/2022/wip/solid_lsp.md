## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) SOLID 3/5 - Le LSP

---

### Introduction

Le **LSP** (**L**iskov **S**ubstitution **P**rinciple, ou principe de substitution de Liskov) est l'un des 5 principes **SOLID**.

Il peut être énoncé comme suit :

> Tout invariant inhérent à un type `T` doit être valide pour un type `U` si `U` veut se substituer à `T`. De plus, les préconditions propres à `T` ne peuvent pas être renforcées dans `U` et les postconditions de `T` ne peuvent pas être affaiblies dans `U`.

Pour faire simple, si une classe `Derived` hérite d'une classe `Parent`, alors toute instance de `Derived` peut être utilisée en tant que `Parent`, de la même manière et sans altérer son comportement.
Toutefois, cela ne s'applique pas qu'à la POO, mais également à la programmation générique.

---

### Exemple

Voici un exemple de non-respect du LSP, ici en C#.
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

Puis, imaginons que l'on ajoute une méthode à notre type `Rectangle` :
```c#
// Toujours un simple rectangle
record Rectangle(float Width, float Height) {
    public Rectangle Resize(float w, float h)
        => this with { Width = w, Height = h };
}
```
Ensuite, lors de l'appel à `Resize`, si l'appelant est de type `Square`, que se passe-t-il ? On casse l'essence même de notre carré !

---

### Solutions possibles

- `struct` (pour value objects, non héritables)
- `sealed class`
