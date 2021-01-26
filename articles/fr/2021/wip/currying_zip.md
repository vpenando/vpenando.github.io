## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Un peu de fonctionnel : curryfication et zip

---

### Introduction

---

### Exemple
Explication :

`zip` est une fonction d'ordre supérieur attendant trois arguments : une fonction et deux listes.
Pour chacun des éléments de ces listes, on appelle une fonction intermédiaire et on stocke le résultat de cet appel dans une liste, que l'on retournera.

Implémentation version impérative :
```js
function zip(func, list1, list2) {
  const minLength = Math.min(list1.length, list2.length);
  let result = [];
  for (let i = 0; i < minLength; i++) {
    const zipped = func(list1[i], list2[i]);
    result.push(zipped);
  }
  return result;
}
```
Implémentation version fonctionnelle, ici en F# :
```fsharp
let zip func list1 list2 =
  let rec zip_acc acc fn l1 l2 =
    if l1 = [] || l2 = [] then acc
    else
      let head1, head2 = List.head l1, List.head l2 in
      let zipped = fn head1 head2 in
      let tail1, tail2 = List.tail l1, List.tail l2 in
      zip_acc (acc@[zipped]) fn tail1 tail2
  in zip_acc [] func list1 list2
```
Et avec quelques commentaires :
```fsharp
// notre fonction zip
let zip func list1 list2 =
  // on déclare ici une seconde fonction,
  // similaire à la première mais récursive et avec un
  // "accumulateur" qui stockera nos résultats
  let rec zip_acc acc fn l1 l2 =
    // si on a atteint la fin d'une liste, on retourne acc
    if l1 = [] || l2 = [] then acc
    // sinon, on "zippe" le prochain élément
    else
      let head1, head2 = List.head l1, List.head l2 in // on récupère les premiers éléments...
      let zipped = fn head1 head2 in                   // ... que l'on utilise pour calculer le résultat
      let tail1, tail2 = List.tail l1, List.tail l2 in // et on ne conserve que la suite des listes
      zip_acc (acc@[zipped]) fn tail1 tail2 // on rappelle zip_acc en rajoutant zipped à la liste de résultats !
  in zip_acc [] func list1 list2
```
La ligne la plus obscure est sans aucun doute la suivante :


On ne dirait pas, mais la seconde version *-bien qu'apparemment plus verbeuse-* est bien plus facilement réutilisable que la première.
Cela est dû au fait que l'on peut utiliser l'*application partielle* de fonction en F# !

Exemple :
```fsharp
// on appelle zip avec seulement un argument !
// sum_lists est une fonction n'attendant plus que deux arguments
let sum_lists = zip (fun x y -> x + y)
```
