## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) La valeur null, un réel intérêt ?

---

### Sommaire
* Introduction
* Problème
* Solutions
* Mais du coup, c'est vraiment utile ?

---

### Introduction
Beaucoup de langages ont une valeur nulle, telle que `null` en C#, `None` en Python, `nil` en Ruby ou Lua (sans oublier `NULL` en C et `nullptr` en C++ lorsque l'on utilise des pointeurs).
Cette valeur a toujours la même sémantique, à savoir l'absence de valeur. Cela peut avoir ses avantages, comme lorsque l'on veut proposer des paramètres optionnels pour une fonction :

```py
def say_hello(name=None):
    if name is not None:
        print("Hello, {}!".format(name))
    else:
        print("Hello, world!")
```

Mais une valeur nulle pose également bon nombre de soucis, car elle impose par nature des préconditions supplémentaires.

---

### Problème
Qui n'a jamais été confronté à la fameuse `NullReferenceException` en C# ?

Je suis souvent amené à maintenir du code C# où chaque méthode commence par une suite de `Debug.Assert(xxx != null, "Invalid parameter xxx")`. Cela induit que là où la valeur nulle est *théoriquement* acceptée (pour chaque type référence, en soi), son usage n'est pas toléré. Chaque type référence (contrairement aux types valeurs), en C#, accepte cette valeur :
```cs
string toto = null;  // ok, type référence
int titi = null;     // ko, type valeur
```
Si le code n'est pas blindé, il devient alors assez facile de le faire crasher en envoyant `null` en lieu et place d'un paramètre d'une fonction / méthode. Chaque programmeur doit donc prendre en compte ce cas de figure, ce qui multiplie rapidement le nombre de vérifications à faire. Cela devient donc vite très lourd, et plus rapidement source d'erreurs.

---

### Solutions

1. C# - Nullable reference types (C# 8)
```cs
string s = null; // Warning: Assignment of null to non-nullable reference type
```
Microsoft ayant réalisé les différents problèmes soulevés par `null` (#BalanceTaNullReferenceException), ils ont décidé de rendre explicite l'usage de types nullables *via* l'obligation d'employer le suffixe `?`. `string` deviendrait donc non-nullable, là où `string?` reste compatible. Pour rappel, `T?` est juste une syntaxe pour `Nullable<T>`.

***Note -** Pour des raisons de rétrocompatibilité, assigner `null` à un type référence non-nullable produit juste un warning. Cependant, je suggère de toujours traiter les warnings comme des erreurs (sous Visual Studio : Project > Properties > Build > Treat Warnings as errors).*

Paradoxalement, C# 6 a permis l'arrivée de l'opérateur `?.` (aka Null Propagation Operator), qui empêche le code suivant de planter :
```cs
class Foo {
    public string Bar { get; }
    
    // ...
}

// ...

Foo f = null;
var result = f?.Bar;  // pas de NullReferenceException !
                      // result vaudra simplement null
                      // car l'expression f?.Bar renvoie null
```
Sans traiter les warnings comme erreurs (cf. la note ci-dessus), nous nous exposons à nouveau à recevoir `null` comme valeur pour `result`.

---

### Mais du coup, c'est vraiment utile ?
Au vu des potentiels soucis causé par la valeur nulle, nous nous devons de poser la question suivante :
> De quel côté penche la balance lorsque l'on mesure l'intérêt de la valeur nulle et ses désagréments ?

A mon sens, sans hésiter, `null` pose plus de problèmes qu'elle n'en résout. Limiter son usage comme le propose Microsoft pour C# 8 me semble être une bonne alternative. Bien qu'utile dans certains cas, elle nous oblige à être extrêmement vigilants.
