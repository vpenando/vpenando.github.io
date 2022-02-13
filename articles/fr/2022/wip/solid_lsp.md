## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) SOLID 3/5 - Le LSP

---

### Introduction

> Tout invariant inhérent à un type `T` doit être valide pour un type `U` si `U` veut se substituer à `T`. Cela induit que les préconditions propres à `T` ne peuvent pas être renforcées dans `U` et que les postconditions de `T` ne peuvent pas être affaiblies dans `U`.

Cela ne s'applique pas qu'à la POO, mais également à la programmation générique.

---

### Exemple 


Exemple de non-respect du LSP, ici en C# :
```c#
record Rectangle(float Width, float Height);

record Square(float Side) : Rectangle(Side, Side);

// Plus loin dans le code...

Rectangle ResizeRectangleWidth(Rectangle rectangle, float newWidth)
    => rectangle with { Width = newWidth };
    
Rectangle ResizeRectangleHeight(Rectangle rectangle, float newHeight)
    => rectangle with { Height = newHeight };
```

Avant toute chose, il ne fait aucun sens qu'un carré possède une largeur et une hauteur, car tous ses côtés doivent avoir la même taille (précondition renforcée !).
Lors de l'appel à `ResizeRectangleWidth` ou `ResizeRectangleHeight` en passant un objet de type `Square`, que se passe-t-il ? On casse l'essence même de notre type `Square` !


---

### Solutions possibles

- `struct`
- `sealed class`
