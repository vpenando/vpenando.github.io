## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Un peu de fonctionnel : curryfication et zip

---

### Introduction

---

### Le problème

Implémentation naïve de `zip` :

```fsharp
let zip list1 list2 =
  let rec zip_acc acc l1 l2 =
    if l1 = [] || l2 = [] then acc
    else
      let head1, head2 = List.head l1, List.head l2 in
      let result = (head1, head2) in
      let tail1, tail2 = List.tail l1, List.tail l2 in
      zip_acc (acc@[result]) tail1 tail2
  in zip_acc [] list1 list2
```
Et à l'usage :
```fsharp
let zipped = zip [1; 2; 3] [4; 5; 6]
// zipped : [(1, 4); (2, 5); (3, 6)]
```
Dans cet exemple, les appels successifs à `zip_acc` seront les suivants :
* Premier appel : `acc` vaut `[]`, `l1` vaut `[1; 2; 3]` et `l2` vaut `[4; 5; 6]` ;
  * Aucune liste n'est vide, on continue les appels à `zip_acc`.
* Second appel : `acc` vaut `[(1, 4)]`, `l1` vaut `[2; 3]` et `l2` vaut `[5; 6]` ;
  * Aucune liste n'est vide, on continue les appels à `zip_acc`.
* Troisième appel : `acc` vaut `[(1, 4); (2, 5)]`, `l1` vaut `[3]` et `l2` vaut `[6]` ;
  * Aucune liste n'est vide, on continue les appels à `zip_acc`.
* Quatrième appel : `acc` vaut `[(1, 4); (2, 5); (3, 6)]`, `l1` vaut `[]` et `l2` vaut `[]` ;
  * Au moins l'une des deux listes `l1` et `l2` est vide : fin de récursion.

La fonction `zip` ci-dessus n'est absolument pas générique.
Si l'on veut créer une nouvelle fonction faisant par exemple, la somme de deux listes, 98% du code seront similaires.

---

### Résolution
Solution :
Imaginons une fonction d'ordre supérieur attendant trois arguments : une fonction et deux listes.
Pour chacun des éléments de ces listes, on appelle une fonction intermédiaire et on stocke le résultat de cet appel dans une liste, que l'on retournera.

Implémentation version impérative :
```js
// on applique une fonction à deux listes
function applyOnLists(func, list1, list2) {
  const minLength = Math.min(list1.length, list2.length);
  let results = [];
  for (let i = 0; i < minLength; i++) {
    const result = func(list1[i], list2[i]);
    results.push(result);
  }
  return results;
}
```
Implémentation version fonctionnelle, ici en F# :
```fsharp
let apply_on_lists func list1 list2 =
  // fonction interne, car on ne veut pas l'exposer
  // à d'autres potentiels appelants
  let rec apply_on_lists_acc acc fn l1 l2 =
    // si au moins une liste est vide, on renvoie acc
    if l1 = [] || l2 = [] then acc
    else
      // on récupère les premiers éléments...
      let head1, head2 = List.head l1, List.head l2 in
      // ...que l'on utilise pour calculer le résultat
      let result = fn head1 head2 in
      // et on ne conserve que la suite des listes
      let tail1, tail2 = List.tail l1, List.tail l2 in
      // on rappelle apply_on_lists_acc en rajoutant result aux résultats !
      apply_on_lists_acc (acc@[result]) fn tail1 tail2
  in apply_on_lists_acc [] func list1 list2
```
De prime abord, on constate que nous utilisons la récursivité plutôt qu'une boucle.
En effet, en fonctionnel, nous ne cherchons pas à modifier une variable ; c'est l'un des principes clés du paradigme. Tout est par défaut constant.
Ainsi, le débogage de nos programmes est plus simple car nous n'avons nul besoin de chercher où nos variables seront modifiées.

On ne dirait pas, mais la seconde version *-bien qu'apparemment plus verbeuse-* est bien plus facilement réutilisable que la première.
Cela est dû au fait que l'on peut utiliser l'*application partielle* de fonction en F# !

Vous ne me croyez pas ? Démonstration :
```fsharp
// on peut appeler apply_on_lists avec seulement un argument pour faire de nouvelles fonctions !

// notre fonction zip, comme vue plus haut
let zip = apply_on_lists (fun x y -> (x, y))
let zipped = zip [1; 2; 3] [4; 5; 6]
// zipped : [(1, 4); (2, 5); (3, 6)]

// une fonction qui ajoute les éléments d'une liste deux à deux
let add_lists = apply_on_lists (fun x y -> x + y)
let added = add_lists [1; 2; 3] [4; 5; 6]
// added : [5; 8; 9]
```
De plus, cette fonction *-ainsi que ses applications partielles !-* est générique :
```fsharp
let hello_world = add_lists ["Hello, "] ["world!"]
// hello_world : ["Hello, world!"]
```
