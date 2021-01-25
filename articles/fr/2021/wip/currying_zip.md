## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Un peu de fonctionnel : curryfication et zip

---

### Introduction

---

### Exemple

```ml
(* on attend trois paramètres :
 * func, la fonction qui 'zippe' nos listes
 * list1, la première liste
 * list2, la deuxième liste
 *)
let zip func list1 list2 =
  (* on déclare ici une seconde fonction,
   * similaire à la première
   *)
  let rec zip_acc acc fn l1 l2 =
    if l1 = [] || l2 = [] then acc
    else
      let head1, head2 = List.head l1, List.head l2 in
      let tail1, tail2 = List.tail l1, List.tail l2 in
      let elem = fn head1 head2 in
      zip_acc (acc@[elem]) fn tail1 tail2
  in zip_acc [] func list1 list2
```
Et à l'usage :
```fs
let sum_lists = zip (fun x y -> x + y)
```
