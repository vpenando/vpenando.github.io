## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) La valeur nulle, un réel intérêt ?

---

### Sommaire
* [Introduction](https://github.com/vpenando/vpenando.github.io/blob/master/articles/fr/2019/la_valeur_nulle.md#introduction)
* [Problème](https://github.com/vpenando/vpenando.github.io/blob/master/articles/fr/2019/la_valeur_nulle.md#probl%C3%A8me)
* [Solutions](https://github.com/vpenando/vpenando.github.io/blob/master/articles/fr/2019/la_valeur_nulle.md#solutions)
  * [1. C# - Nullable reference types (C# 8)](https://github.com/vpenando/vpenando.github.io/blob/master/articles/fr/2019/la_valeur_nulle.md#1-c---nullable-reference-types-c-8)
  * [2. C++ - `std::optional` (C++17 / Boost)](https://github.com/vpenando/vpenando.github.io/blob/master/articles/fr/2019/la_valeur_nulle.md#2-c---stdoptional-c17--boost)
  * [3. Et dans d'autres langages ?](https://github.com/vpenando/vpenando.github.io/blob/master/articles/fr/2019/la_valeur_nulle.md#3-et-dans-dautres-langages-)
* [Mais du coup, une valeur nulle, c'est vraiment utile ?](https://github.com/vpenando/vpenando.github.io/blob/master/articles/fr/2019/la_valeur_nulle.md#mais-du-coup-une-valeur-nulle-cest-vraiment-utile-)

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

Mais une valeur nulle pose également bon nombre de soucis, car elle impose par nature des préconditions supplémentaires. En effet, que se passerait-il si l'on tente d'utiliser une variable nulle ?

---

### Problème
Qui n'a jamais été confronté à la fameuse `NullReferenceException` en C# ?

Je suis souvent amené à maintenir du code C# où chaque méthode commence par une suite de `Debug.Assert(xxx != null, "Invalid parameter xxx")`. Cela induit que là où la valeur nulle est *théoriquement* acceptée (pour chaque type référence, en soi), son usage n'est pas toléré. Chaque type référence (contrairement aux types valeurs), en C#, accepte cette valeur :
```cs
string toto = null;  // ok, type référence
int titi = null;     // ko, type valeur
```
Si le code n'est pas blindé, il devient alors assez facile de le faire crasher en envoyant `null` en lieu et place d'un paramètre d'une fonction ou méthode. Chaque programmeur doit donc prendre en compte ce cas de figure, ce qui multiplie rapidement le nombre de vérifications à faire. Cela devient donc vite très lourd, et plus rapidement source d'erreurs.

---

### Solutions

#### 1. C# - Nullable reference types (C# 8)
```cs
string s = null; // Warning: Assignment of null to non-nullable reference type
```
Microsoft ayant réalisé les différents problèmes soulevés par `null` (#BalanceTaNullReferenceException), ils ont décidé de rendre explicite l'usage de types nullables *via* l'obligation d'employer le suffixe `?`. `string` deviendrait donc non-nullable, là où `string?` reste compatible. Pour rappel, `T?` est juste une syntaxe pour `System.Nullable<T>` :
```cs
int? toto = null;  // ok : "toto" est de type Nullable<int>
```

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
                      // car l'expression "f?.Bar" renvoie null
```
Bien que le but soit très louable (s'éviter des `NullReferenceException`s), je trouve cette solution plutôt maladroite. Elle ne corrige que la forme (le code ne plantera pas), mais pas le fond (on continue à avoir des variables nulles). De ce fait, il finira tôt ou tard par crasher, à un endroit où nous n'aurons pas été suffisamment vigilants.

Sans traiter les warnings comme erreurs (cf. la note ci-dessus), nous nous exposons à nouveau à recevoir `null` comme valeur pour `result`.

#### 2. C++ - `std::optional` (C++17 / Boost)

Je suis développeur C++ avant tout. C'est le langage que j'utilise le plus, et les occasions d'employer les paramètres par défaut ne manquent pas. L'astuce "classique" consiste à utiliser des pointeurs :
```cpp
using Owner = T;  // alias sémantique
                  // cela induit qu'un "Owner<T>" est l'unique
                  // responsable de la ressource tenue.

void foo(int arg1, int arg2, Owner<int*> optional=nullptr){
  // ...
}

// et à l'usage :
int i = 42;
foo(1, 2, &i);
// ou
foo(1, 2);
```
Bien que l'usage soit assez commun en soi, l'utilisation d'un pointeur en lieu et place d'une valeur optionnelle est sémantiquement assez bancale. Rajoutons à cela que l'*ownership* n'est pas clairement définie à la simple lecture du prototype de `foo` (d'où l'usage de l'alias `Owner`, qui l'apporte explicitement).

Fort heureusement, C++17 a rajouté un type (déjà disponible dans Boost) qui apporte toute cette sémantique : `std::optional`. Tout comme pour C# 8, on restreint là encore l'utilisation d'une valeur nulle. L'usage de pointeurs bruts étant déjà déprécié aujourd'hui (au profit de types RAII-conform), on donne aux programmeurs une autre alternative.

Il existe une autre solution pour se passer de l'emploi de pointeurs ou de `std::optional`. En effet, la surcharge de fonction est une option viable :
```cpp
void foo(int arg1, int arg2){
  // ...
}

void foo(int arg1, int arg2, int arg3){
  // ...
}
```

#### 3. Et dans d'autres langages ?
Je fais également un peu de fonctionnel avec OCaml / F#, et j'ai eu l'occasion d'utiliser un peu Elm sur mon temps libre. Ces langages proposent des types proposant des valeurs pseudo-nulles. Exemple :
```ml
(* OCaml / F# *)
let someValue = Some "Hello, world!" ;;

(* "someValue" est de type "string option" *)

let print_if_exists optionalValue =
  match optionalValue with
  | Some value -> print_string value
  | None -> print_string "???"
  ;;
```
Le type `option` correspond, comme son nom l'indique, à une valeur optionnelle. Il accepte de ce fait deux "types" de valeurs : `None` et `Some xxx`, avec `xxx` une valeur.

Il pourrait être implémenté ainsi :
```ml
type 'a option =
  | Some of 'a  (* une valeur de type 'a *)
  | None        (* pas de valeur *)
  ;;
```
Elm dispose d'un mécanisme similaire avec le type `Maybe`, acceptant les valeurs `Just quelquechose` et `Nothing` :
```elm
myFunction maybe =
  case maybe of
    Just value -> -- quelque chose...
    Nothing -> -- autre chose...
```
Dans la même logique que `option`, `Maybe` pourrait être implémenté ainsi :
```elm
type Maybe a
    = Just a
    | Nothing
```
L'intérêt de ce genre de type est que l'on s'interdit de pouvoir rendre toute variable nulle, à moins d'explicitement utiliser un type "nullable". Ainsi, pas de surprise avec des variables nulles.

---

### Mais du coup, une valeur nulle, c'est vraiment utile ?
Au vu des potentiels soucis causés par la valeur nulle, nous nous devons de poser la question suivante :
> De quel côté penche la balance lorsque l'on mesure l'intérêt de la valeur nulle et ses désagréments ?

A mon sens, sans hésiter, `null` pose plus de problèmes qu'elle n'en résout. Limiter son usage comme le propose Microsoft pour C# 8 me semble être une bonne alternative. Bien qu'utile dans certains cas, elle nous oblige à être extrêmement vigilants.
Beaucoup de langages pourraient selon moi s'en passer, ou tout du moins se restreindre à un type "nullable".

Néanmoins, je pense que dans le cas de langages de script, c'est un excellent atout. Je pense notamment à Python, où la surcharge de fonction n'existe pas. Ainsi, pour simuler des paramètres optionnels, une valeur nulle est indispensable. En Lua, tous les paramètres de fonctions sont nuls par défaut :
```lua
function foo(arg1, arg2, arg3)
  print(arg1)
  print(arg2)
  print(arg3)
end

foo(1, 2)
--[[
output :
1
2
nil
]]--
```
JavaScript, par exemple, s'autorise également ce comportement.

Cela contribue à garantir la grande malléabilité du langage, mais la plupart des autres langages (notamment les langages compilés) ne s'autorisent pas cela. Spécifier un paramètre par défaut nul ne présente que rarement un intérêt lorsque l'on a la possibilité de surcharger une fonction. Cela alourdit vite le code si l'on doit enchaîner les `if (xxx == null) { /* ... */ }` pour chaque paramètre : je préfère largement avoir une fonction distincte pour chaque cas, quand c'est possible.

Finalement, le cas des paramètres optionnels constitue **le** cas nominal d'emploi d'une valeur nulle. A-t-on besoin de `null` dans d'autres cas ?
