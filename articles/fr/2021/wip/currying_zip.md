## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Un peu de fonctionnel : curryfication et zip

---

### Introduction

---

### Exemple
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
Implémentation version fonctionnelle :
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

```fsharp
let zip func list1 list2 =
  // on déclare ici une seconde fonction,
  // similaire à la première mais avec un
  // "accumulateur" qui stockera nos résultats
  let rec zip_acc acc fn l1 l2 =
    // si on a atteint la fin d'une liste, on retourne acc
    if l1 = [] || l2 = [] then acc
    // sinon, on "zippe" le prochain élément
    else
      let head1, head2 = List.head l1, List.head l2 in
      let zipped = fn head1 head2 in
      let tail1, tail2 = List.tail l1, List.tail l2 in
      zip_acc (acc@[zipped]) fn tail1 tail2
  in zip_acc [] func list1 list2
```
Explication :

`zip` est une fonction d'ordre supérieur attendant trois arguments : une fonction et deux listes. Pour chacun 

Et à l'usage :
```fsharp
let sum_lists = zip (fun x y -> x + y)
```
