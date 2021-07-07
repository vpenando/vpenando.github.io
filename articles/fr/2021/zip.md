## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Un peu de fonctionnel : application partielle et zip

---

### Introduction
En complément de mon apprentissage de Rust, je m'exerce en programmation fonctionnelle.
Il s'agit d'un paradigme très intéressant, qui offre de nombreuses fonctionnalités extrêmement puissantes.
Parmi elles, il existe la notion d'*application partielle* de fonction.

Cette fonctionnalité permet de résoudre certains problèmes, et c'est précisément ce que nous allons voir dans cet article.
J'emploierai le langage F#, qui est un dérivé d'OCaml, lui-même un excellent langage.

---

### Le problème
Le problème posé sera le suivant : quelle est la "meilleure" façon d'implémenter la fonction `zip` ?

Pour rappel, la fonction `zip` attend deux listes et renvoie une liste de tuples contenant les éléments des deux arguments regroupés deux à deux.
Son comportement est proche de :
```fsharp
let result = zip [a; c; e] [b; d; f]
// result = [(a, b); (c, d); (e, f)]
```
Une implémentation naïve de `zip` pourrait être la suivante :
```js
function tuple(x, y) {
  // l'implémentation ne nous intéressera pas ici
  // disons juste qu'elle renvoie un tuple
}

function zip(list1, list2) {
  const minLength = Math.min(list1.length, list2.length);
  let results = [];
  for (let i = 0; i < minLength; i++) {
    const result = tuple(list1[i], list2[i]);
    results.push(result);
  }
  return results;
}
```
L’implémentation impérative est très simple à comprendre : on itère sur nos listes jusqu’à avoir atteint la fin de l’une d’elles. À chaque itération, on stocke le résultat de notre calcul dans une troisième liste, que l’on renvoie.

L’implémentation version fonctionnelle diffère quelque peu :
```fsharp
let zip list1 list2 =
  let rec zip_acc acc l1 l2 =
    if l1 = [] || l2 = [] then acc
    else
      let head1, head2 = List.head l1, List.head l2 in
      let result = (head1, head2) in
      let tail1, tail2 = List.tail l1, List.tail l2 in
      let new_acc = acc @ [result] in
      zip_acc new_acc tail1 tail2
  in zip_acc [] list1 list2
```
De prime abord, on constate que nous utilisons la récursivité plutôt qu’une boucle. En effet, en fonctionnel, nous ne cherchons pas à modifier une variable ; c’est l’un des principes clés du paradigme. Tout est par défaut constant. Ainsi, le débogage de nos programmes est plus simple car nous n’avons nul besoin de chercher où nos variables seront modifiées.

Et à l'usage :
```fsharp
let zipped = zip [1; 2; 3] [4; 5; 6]
// zipped : [(1, 4); (2, 5); (3, 6)]
```
***Note -** La fonction `List.head` renvoie le premier élément d'une liste.
`List.tail` renvoie quant à elle tous les élements d'une liste à l'exception du premier.*

Dans cet exemple, les appels successifs à `zip_acc` seront les suivants :
* Premier appel :
  * `acc` vaut `[]` ;
  * `l1` vaut `[1; 2; 3]` ;
  * `l2` vaut `[4; 5; 6]` ;
  * Ni `l1` ni `l2` n'est vide, on continue les appels à `zip_acc`.
* Second appel :
  * `acc` vaut `[(1, 4)]` ;
  * `l1` vaut `[2; 3]` ;
  * `l2` vaut `[5; 6]` ;
  * Ni `l1` ni `l2` n'est vide, on continue les appels à `zip_acc`.
* Troisième appel :
  * `acc` vaut `[(1, 4); (2, 5)]` ;
  * `l1` vaut `[3]` ;
  * `l2` vaut `[6]` ;
  * Ni `l1` ni `l2` n'est vide, on continue les appels à `zip_acc`.
* Quatrième appel :
  * `acc` vaut `[(1, 4); (2, 5); (3, 6)]` ;
  * `l1` vaut `[]` ;
  * `l2` vaut `[]` ;
  * Au moins l'une des deux listes `l1` et `l2` est vide : fin de récursion.

Néanmoins, la fonction `zip` ci-dessus pose un petit problème.
En effet, si l'on veut créer une nouvelle fonction faisant par exemple, la somme de deux listes, 98% du code seront similaires.
Il serait donc intéressant d'avoir une partie générique, que l'on spécialiserait en fonction de nos différents cas d'usage.

---

### Résolution
Si vous avez lu le titre de l'article (:D), vous avez une idée de la solution.

Imaginons une fonction d'ordre supérieur attendant trois arguments : une fonction et deux listes.
Pour chacun des éléments de ces listes, on appelle cette fonction intermédiaire et on stocke le résultat de cet appel dans une liste, que l'on retournera.

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
Implémentation version F# :
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
      // on ajoute 'result' au nouvel accumulateur
      let new_acc = acc @ [result] in
      // et enfin, on rappelle apply_on_lists_acc !
      apply_on_lists_acc new_acc fn tail1 tail2
  in apply_on_lists_acc [] func list1 list2
```
***Note -** Cette fonction est drôlement similaire à [`map2`](https://fsharp.github.io/fsharp-core-docs/reference/fsharp-collections-listmodule.html#map2), n'est-ce pas ?*

On ne dirait pas, mais la seconde version *-bien qu'apparemment plus verbeuse-* est bien plus facilement réutilisable que la première.
Cela est dû au fait que l'on peut utiliser l'*application partielle* de fonction en F# !
L'application partielle consiste à appeler une fonction avec seulement *une partie* de ses arguments.
En résulte *une nouvelle fonction*, qui attend le reste des arguments.

Démonstration :
```fsharp
// notre fonction zip, comme vue plus haut
let zip = apply_on_lists (fun x y -> (x, y))
// à l'usage :
let zipped = zip [1; 2; 3] [4; 5; 6] // [(1, 4); (2, 5); (3, 6)]

// une fonction qui ajoute les éléments d'une liste deux à deux
let add_lists = apply_on_lists (fun x y -> x + y)
// à l'usage :
let added = add_lists [1; 2; 3] [4; 5; 6] // [5; 7; 9]
```
De plus, cette fonction *-ainsi que ses applications partielles !-* est générique :
```fsharp
// on l'appelle avec une "string list" plutôt qu'une "int list"
let hello_world = add_lists ["Hello, "] ["world!"] // ["Hello, world!"]
```
L'application partielle est très utile dans ce genre de cas, car elle permet de centraliser un comportement au sein d'une seule et même fonction, qui sera spécialisée par la suite.
Cela permet d'éviter de dupliquer du code, tout en gagnant en lisibilité ; une fois que l'on a compris ce que fait `apply_on_lists `, l'usage de `zip ` et `add_lists` devient limpide.

---

### Conclusion
Tout développeur le sait, il est crucial d'éviter de dupliquer du code. En effet, cela pose plusieurs problèmes, comme par exemple du côté de la maintenabilité (principe [DRY](https://fr.wikipedia.org/wiki/Ne_vous_r%C3%A9p%C3%A9tez_pas)).
Toutefois, il est difficile de ne pas dupliquer du code quand on doit écrire deux fonctions très similaires.

En programmation fonctionnelle, l'application partielle de fonction permet de résoudre ce problème sans ajouter une couche de complexité supplémentaire ; une fois notre fonction d'ordre supérieur définie, déclarer chaque spécialisation prend littéralement *une* ligne !
