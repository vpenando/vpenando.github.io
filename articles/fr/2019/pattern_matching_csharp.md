## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Du pattern matching en C# ! (C# 8)

---

### Sommaire
* Introduction


---

### Introduction
En programmation fonctionnelle, il existe la notion de pattern matching :
```fs
let match_with_42 x =
  match x with
  | 42 -> // ...
  | _  -> // ...
  ;;
```
Cette instruction est fortement similaire au `switch` que nous avons dans la plupart des langages :
```cs
void MatchWith42(int x) {
    switch (x) {
    case 42:
        // ...
        break;
    default:
        // ...
    }
}
```
Néanmoins, le pattern matching permet de décomposer des entités, comme par exemple :
```fs
// cette fonction renvoie une valeur optionnelle :
// 'Some X' ou 'None'
let head_of_list ls =
  match ls with
  | []   -> None
  | x::_ -> Some x
  ;;
```
