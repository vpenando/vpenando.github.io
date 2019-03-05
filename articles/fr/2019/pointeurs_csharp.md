## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Des pointeurs en C# !

---

### Sommaire
* [Introduction](#introduction)

---

### Introduction
***Note - Cet article traitera de C# avancé**. Nous aborderons des notions telles que le code *unsafe*, les blocs `fixed (...)`, ou encore l'utilisation du mot-clé `stackalloc` (oui oui, je parle bien de C#) ; aussi je vous conseille de vous renseigner sur ces points avant de lire la suite !*

Alors que l'utilisation des pointeurs est dépréciée en C++ au profit de capsules RAII-conform (`std::string` plutôt que `const char*`, `std::vector<T>`/`std::array<SIZE, T>` plutôt que `T[]`, `std::unique_ptr<T>`/`std::shared_ptr<T>` plutôt que `T*`...), elle peut être d'une grande utilité en C#. Comme nous allons le voir dans la suite de cet article, utiliser des pointeurs nous permet de nous affranchir de l'overhead causé par les diverses couches appelées lorsque nous invoquons une méthode.

La question du RAII ne se pose pas du fait que les pointeurs créés en C# entrent dans deux catégories :
* D'un côté, on pointe sur des variables déjà existantes *via* `fixed` => pas d'allocation, le GC s'occupe de tout ;
* De l'autre, les pointeurs alloués sur la pile *via* `stackalloc` => sur la pile, pas de libération nécessaire.

Contrairement à ce que l'on pourrait faire en C, nous n'allouons à aucun moment un pointeur sur le tas ; aucune libération n'est donc nécessaire.

Pour rappel, l'utilisation de pointeurs en C ressemble à ceci :
```c
#define ARRAY_SIZE 10
// ...

int *array = malloc(sizeof(*array) * ARRAY_SIZE);  // allocation manuelle
if (! array) {
  // l'allocation a échoué
  // traitement de l'erreur...
}
// utilisation...
// ...
free(array);  // libération manuelle
```

---

### Exemple
Soit une classe `Matrix` représentant une matrice mathématique.
```cs
sealed class Matrix {
    // tableau interne
    private double[] buffer;

    // dimensions
    public uint Height { get; private set; }
    public uint Width { get; private set; }

    // accesseurs en lecture et écriture
    public double this[uint y, uint x] {
        get { return this.buffer[this.CoordsToIndex(y, x)]; }
        set { this.buffer[this.CoordsToIndex(y, x)] = value; }
    }

    public Matrix(uint height, uint width) {
        this.Height = height;
        this.Width = width;
        this.buffer = new double[this.Width * this.Height];
    }
    
    // sera utilisé par la suite
    private Matrix() {
        this.Height = 0;
        this.Width = 0;
        this.buffer = null;
    }

    // convertit un couple de coordonnées X et Y en index unidimensionnel
    private uint CoordsToIndex(uint y, uint x) => y * this.Width + x;
}
```

Addition :
```cs
// version lente
public static Matrix SafeAdd(Matrix lhs, Matrix rhs) {
    Debug.Assert(lhs.Width == rhs.Width, "Cannot perform operation on this matrices");
    Debug.Assert(lhs.Height == rhs.Height, "Cannot perform operation on this matrices");
    var result = new Matrix(lhs.Height, lhs.Width);
    for (var i = 0; i < lhs.buffer.Count(); ++i) {
        result.buffer[i] = lhs.buffer[i] + rhs.buffer[i];
    }
    return result;
}

// version optimisée
public static Matrix UnsafeAdd(Matrix lhs, Matrix rhs) {
    Debug.Assert(lhs.Width == rhs.Width, "Cannot perform operation on this matrices");
    Debug.Assert(lhs.Height == rhs.Height, "Cannot perform operation on this matrices");
    var count = lhs.buffer.Count();
    var result = new double[count];
    unsafe {
        fixed (double*
            rawDst = result,
            rawLhs = lhs.buffer,
            rawRhs = rhs.buffer)
        {
            RawAddMatrices(/*out*/ rawDst, rawLhs, rawRhs, count);
        }
    }
    var matrix = new Matrix();
    matrix.buffer = result;
    matrix.Width = lhs.Width;
    matrix.Height = lhs.Height;
    return matrix;
}

// attention !
// nous n'avons ici AUCUN contrôle des bornes !
// il faut donc fournir des paramètres corrects.
private unsafe static void RawAddMatrices(
    double *dst,  // matrice de destination
    double *lhs,  // opérande de gauche
    double *rhs,  // opérande de droite
    int count)    // nombre d'éléments
{
    Debug.Assert(dst != null, "Null pointer");
    Debug.Assert(lhs != null, "Null pointer");
    Debug.Assert(rhs != null, "Null pointer");
    Debug.Assert(count > 0, "Invalid array size");
    for (var i = 0; i < count; ++i) {
        *dst = *lhs + *rhs;
        ++dst;
        ++rhs;
        ++lhs;
    }
}
```

Benchmarks d'additions de matrices de 5000x5000 élements :
```sh
(Matrix.SafeAdd)   Time: 1169,6524 ms
(Matrix.UnsafeAdd) Time: 132,9075 ms
```
9000x9000 :
```sh
(Matrix.SafeAdd)   Time: 2234.8486 ms
(Matrix.UnsafeAdd) Time: 350.5943 ms
```` 
