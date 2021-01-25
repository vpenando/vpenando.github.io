## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Un peu de fonctionnel : curryfication et zip

---

### Introduction

---

### Exemple

```fs
let zip func list1 list2 =
  let rec zip_acc acc fn l1 l2 =
    if l1 = [] || l2 = [] then acc
    else
      let head1, head2 = List.head l1, List.head l2 in
      let tail1, tail2 = List.tail l1, List.tail l2 in
      let elem = fn head1 head2 in
      zip_acc (elem::acc) fn tail1 tail2
  in zip_acc [] func list1 list2 |> reverse
```
Et à l'usage :
```fs
let sum_lists = zip (fun x y -> x + y)
```
