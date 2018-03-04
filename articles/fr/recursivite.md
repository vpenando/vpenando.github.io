## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### [FR] Amusons-nous avec la récursivité (F#)

##### Introduction
Cet article est une réflexion personnelle. Il ne s'agit pas d'un article structuré visant à apprendre ou détailler des points du langage. Par ailleurs, je compile en C++14, aussi je vous recommande d'utiliser un compilateur supportant cette norme si vous souhaitez reproduire ces exemples (VS2015/2017 ou un GCC/Clang récent).

#### 1. Récursivité VS boucles

##### a. La somme d'une liste
```cpp
int sum(std::vector<int> const& vec){
  int result = 0;
  for(auto it = vec.begin(); it != vec.end(); ++it){
    result += *it;
  }
  return result;
}
```

###### Possible implémentation :
```fs
let rec sum list =
  match list with
    | []    -> 0
    | x::xs -> x + (sum xs)
```
***Note*** - *L'expression `x::xs` correspond à la liste décomposée en `x` (son premier élément) et `xs` (le reste de la liste).*
Ou plus simplement :
```fs
let rec sum = function
  | []    -> 0
  | x::xs -> x + (sum xs)
```
Soit une fonction qui attend un argument (ici une liste), et la compare à deux cas :
* Si la liste est vide, on renvoie 0 ;
* Sinon, on prend son premier élément (ici `x`) et on l'additionne au résultat de `sum` sur le reste de la liste (ici `xs`).

##### b. Le cas de la fonction puissance
```cpp
// Via une boucle :
int pow(int n, int p){
  if (p == 0)
    return 1;
  int res = 1;
  for (int i = 0; i < p; i++)
    res *= n;
  return res;
}

// Version récursive :
int pow(int n, int p){
  if (p == 0)
    return 1;
  return n * pow(n, p-1);
}
```
La seconde version est un peu plus concise que la première. Néanmoins, peut-on faire encore plus court ? (et plus élégant)
```fs
let rec pow n p =
  match p with
    | 0 -> 1
    | _ -> n * (pow n (p-1))
```


