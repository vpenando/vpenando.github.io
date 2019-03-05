## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Des pointeurs en C# !

---

### Sommaire
* [Introduction](#introduction)

---

### Introduction

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
    for (int i = 0; i < lhs.buffer.Count(); ++i) {
        result.buffer[i] = lhs.buffer[i] + rhs.buffer[i];
    }
    return result;
}

// version optimisée
public static Matrix UnsafeAdd(Matrix lhs, Matrix rhs) {
    Debug.Assert(lhs.Width == rhs.Width, "Cannot perform operation on this matrices");
    Debug.Assert(lhs.Height == rhs.Height, "Cannot perform operation on this matrices");
    var count = lhs.buffer.Count();
    double[] result = new double[count];
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
    for (var i = 0; i < count; ++i)
    {
        *dst = *lhs + *rhs;
        ++dst;
        ++rhs;
        ++lhs;
    }
}
```
