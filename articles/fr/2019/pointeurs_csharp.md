## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Des pointeurs en C# !

---

### Sommaire
* Introduction
* Pointeurs et performances
* Allocation manuelle de mémoire ?
* Conclusion

---

### Introduction
***Note - Cet article traitera de C# avancé**. Nous aborderons des notions telles que le code "unsafe", les blocs `fixed (...)`, ou encore l'utilisation du mot-clé `stackalloc` (oui oui, je parle bien de C#) ; aussi je vous conseille de vous renseigner sur ces points avant de lire la suite !*

Alors que l'utilisation des pointeurs est dépréciée en C++ au profit de capsules RAII-conform (`std::string` plutôt que `const char*`, `std::vector<T>`/`std::array<SIZE, T>` plutôt que `T[]`, `std::unique_ptr<T>`/`std::shared_ptr<T>` plutôt que `T*`...), elle peut être d'une grande utilité en C#. Comme nous allons le voir dans la suite de cet article, utiliser des pointeurs nous permet de nous affranchir de l'overhead causé par les diverses couches appelées lorsque nous invoquons une méthode.

La question du RAII ne se pose pas du fait que les pointeurs créés en C# entrent dans l'une de ces deux catégories :
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
Autant dire que ça n'est pas de tout confort, n'est-ce pas ?

Rassurez-vous, l'utilisation de pointeurs en C# n'est pas aussi complexe.

---

### Pointeurs et performances
Soit une classe `Matrix` représentant une matrice mathématique :
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

    // convertit un couple de coordonnées en index unidimensionnel
    private uint CoordsToIndex(uint y, uint x) {
        Debug.Assert(y < this.Height, $"Out of bounds! (Y: {y}, Height: {this.Height})");
        Debug.Assert(x < this.Width, $"Out of bounds! (X: {x}, Width: {this.Width})");
        return y * this.Width + x;
    }
}
```
Elle contient des éléments situés à des coordonnées définies et un tableau interne les contenant. Une petite astuce pour gagner en performances consiste en n'avoir qu'un tableau unidimentionnel plutôt qu'un tableau de tableaux (`double[][]`) et une méthode "convertissant" des coordonnées X et Y en index. Les données étant linéarisées en mémoire, les accès sont ainsi plus rapides.

Nous pourrions implémenter une méthode d'addition ainsi (qui serait ensuite appelée par `Matrix.operator+`) :
```cs
public static Matrix SafeAdd(Matrix lhs, Matrix rhs) {
    // préconditions
    Debug.Assert(lhs.Width == rhs.Width, "Cannot perform operation on these matrices");
    Debug.Assert(lhs.Height == rhs.Height, "Cannot perform operation on these matrices");
    // ok !
    var result = new Matrix(lhs.Height, lhs.Width);
    for (var i = 0; i < result.buffer.Count(); ++i) {
        result.buffer[i] = lhs.buffer[i] + rhs.buffer[i];
    }
    return result;
}
```

Mais vous vous en doutez, il y a mieux ! Une méthode plus rapide serait d'utiliser des pointeurs ! Nous outrepassons ainsi les diverses couches de .NET appelées par le code ci-dessus !
```cs
// version optimisée
public static Matrix UnsafeAdd(Matrix lhs, Matrix rhs) {
    // préconditions
    Debug.Assert(lhs.Width == rhs.Width, "Cannot perform operation on these matrices");
    Debug.Assert(lhs.Height == rhs.Height, "Cannot perform operation on these matrices");
    // ok !
    var count = lhs.buffer.Count();
    var result = new double[count];
    // nous entrons dans un contexte unsafe
    unsafe {
        // heureusement, on ne fait rien de bien compliqué :)
        fixed (double*
            rawDst = result,      // ce tableau sera rempli avec le résultat attendu
            rawLhs = lhs.buffer,  // en soi, on pointe sur &lhs.buffer[0]
            rawRhs = rhs.buffer)  // pareil ici, on pointe sur &rhs.buffer[0]
        {
            AddRawMatrices(/*out*/ rawDst, rawLhs, rawRhs, count);
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
private unsafe static void AddRawMatrices(
    double *dst,  // matrice de destination
    double *lhs,  // opérande de gauche
    double *rhs,  // opérande de droite
    int count)    // nombre d'éléments
{
    // préconditions
    Debug.Assert(dst != null, "Null pointer");
    Debug.Assert(lhs != null, "Null pointer");
    Debug.Assert(rhs != null, "Null pointer");
    Debug.Assert(count > 0, "Invalid array size");
    // ok !
    for (var i = 0; i < count; ++i) {
        *dst = *lhs + *rhs;
        ++dst;
        ++rhs;
        ++lhs;
    }
}
```
Les parties qui nous intéressent ici sont la méthode `AddRawMatrices` et le code contenu dans le bloc `unsafe {}`. Examinons-les de plus près.

La méthode `AddRawMatrices`, une fois nettoyée de ses fioritures, se limite à ceci :
```cs
unsafe void AddRawMatrices(
    double *dst,  // matrice de destination
    double *lhs,  // opérande de gauche
    double *rhs,  // opérande de droite
    int count)    // nombre d'éléments
{
    for (var i = 0; i < count; ++i) {
        *dst = *lhs + *rhs;
        ++dst;
        ++rhs;
        ++lhs;
    }
}
```
Le mot-clé `unsafe` indique que nous entrons dans une partie de code "risquée" ; pas de vérification par le CLR. Mais surtout, il est nécessaire d'être dans un bloc unsafe pour utiliser des pointeurs !
Sinon, d'un point de vue algorithmique, on se contente d'itérer sur des pointeurs un nombre de fois défini (à savoir `count`) et de copier la somme des différentes valeurs successivement pointées par `lhs` et `rhs` dans `dst`. Rien de bien sorcier ici.

La partie la plus intéressante est la suivante :
```cs
unsafe {
    fixed (double*
        rawDst = result,
        rawLhs = lhs.buffer,
        rawRhs = rhs.buffer)
    {
        AddRawMatrices(/*out*/ rawDst, rawLhs, rawRhs, count);
    }
}
```
C'est véritablement ici que la magie opère.
Avant toute chose, tout comme au-dessus, nous entrons dans un bloc "unsafe".
Un bloc `fixed` empêche le GC de déplacer des variables dont nous pourrions avoir besoin, telles que `result`, `lhs` ou `rhs`. Dans le cas présent, les variables `rawDst`, `rawLhs` et `rawRhs` ont la garantie d'être valides jusqu'à la fin du bloc `fixed`.

En utilisant des pointeurs plutôt qu'un tableau classique, le code est nettement plus rapide, et cette différence est encore plus apparente lorsque nous travaillons sur des gros volumes de données.

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
([Source](https://gist.github.com/vpenando/b83148710b1dec35983c571592e8d1e0?ts=4))

---

### Allocation manuelle de mémoire ?
Il est possible d'allouer manuellement de la mémoire en C# via le mot-clé `stackalloc`. Contrairement à `malloc` en C, la mémoire n'est pas allouée sur le tas mais sur la pile. Elle sera donc naturellement libérée.

**Attention** ! En allouant beaucoup de mémoire, on risque la `StackOverflowException`, la pile ayant une taille limitée.

Exemple d'allocation sur la pile :
```cs
int *array = stackalloc int[100];
```
Manipuler un tableau alloué avec `stackalloc` est généralement plus lent qu'avec un tableau classique (`int[]`) ; c'est [lorsque l'on traite de gros volumes de données](https://gist.github.com/goldshtn/7021608) que le gain de performance est présent. ([Benchmark](https://gist.github.com/vpenando/b18a7720ee23d8436a266b6ca63a2612))

---

### Conclusion
D'une manière générale, il est très rare d'avoir recours à du code "unsafe". Manipuler directement la mémoire via des pointeurs reste exceptionnel et propre à un besoin précis, tel que du traitement d'image, ou encore des calculs matriciels. La plupart des utilisations du C# ne nécessite habituellement pas un tel niveau d'optimisation.

Néanmoins, il est intéressant de se renseigner sur ces fonctionnalités du langage, ne serait-ce que par cusiosité !
