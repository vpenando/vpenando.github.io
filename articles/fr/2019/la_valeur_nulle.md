## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) La valeur null, un réel intérêt ?

---

### Sommaire
* [Introduction](#introduction)
*

---

#### Introduction
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

#### Problème
Je suis souvent amené à maintenir du code C# où chaque méthode commence par une suite de `Debug.Assert(xxx != null, "Invalid parameter xxx")`. Cela induit que là où la valeur nulle est théoriquement *acceptée* (pour chaque type référence, en soi), son usage n'est pas toléré. Chaque type référence (contrairement aux types valeurs), en C#, accepte cette valeur :
```cs
string toto = null;  // ok, type référence
int titi = null;     // ko, type valeur
```
Si le code client n'est pas blindé, il devient alors assez facile de le faire crasher en envoyant `null` en lieu et place d'un paramètre d'une fonction / méthode.


---

#### Solutions
Nullable reference types (C# 8)
```cs
string s = null; // Warning: Assignment of null to non-nullable reference type
```



