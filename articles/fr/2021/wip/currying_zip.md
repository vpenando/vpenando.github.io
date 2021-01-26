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

---

### Exemple
Explication :

Imaginons une fonction d'ordre supérieur attendant trois arguments : une fonction et deux listes.
Pour chacun des éléments de ces listes, on appelle une fonction intermédiaire et on stocke le résultat de cet appel dans une liste, que l'on retournera.

Implémentation version impérative :
```js
// on applique une fonction à deux listes
function apply2(func, list1, list2) {
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
let apply2 func list1 list2 =
  let rec apply2_acc acc fn l1 l2 =
    if l1 = [] || l2 = [] then acc
    else
      let head1, head2 = List.head l1, List.head l2 in
      let result = fn head1 head2 in
      let tail1, tail2 = List.tail l1, List.tail l2 in
      apply2_acc (acc@[result]) fn tail1 tail2
  in apply2_acc [] func list1 list2
```
Et avec quelques commentaires :
```fsharp
// notre fameuse fonction
let apply2 func list1 list2 =
  // on déclare ici une seconde fonction,
  // similaire à la première mais récursive et avec un
  // "accumulateur" qui stockera nos résultats
  let rec apply2_acc acc fn l1 l2 =
    // si on a atteint la fin d'une liste, on retourne acc
    if l1 = [] || l2 = [] then acc
    else
      let head1, head2 = List.head l1, List.head l2 in // on récupère les premiers éléments...
      let result = fn head1 head2 in                   // ...que l'on utilise pour calculer le résultat
      let tail1, tail2 = List.tail l1, List.tail l2 in // et on ne conserve que la suite des listes
      apply2_acc (acc@[result]) fn tail1 tail2 // on rappelle apply2_acc en rajoutant result aux résultats !
  // ici, on effectue le premier appel à apply2_acc :
  // une fois toutes les récursions résolues, on renvoie le résultat
  in apply2_acc [] func list1 list2
```
On ne dirait pas, mais la seconde version *-bien qu'apparemment plus verbeuse-* est bien plus facilement réutilisable que la première.
Cela est dû au fait que l'on peut utiliser l'*application partielle* de fonction en F# !

Exemple :
```fsharp
// on peut appeller apply2 avec seulement un argument pour faire de nouvelles fonctions !
// exemple :
// zip est une fonction n'attendant plus que deux arguments
let zip = apply2 (fun x y -> (x, y))

```
