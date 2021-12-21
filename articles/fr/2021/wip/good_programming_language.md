## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Un *bon* langage de programmation, c'est quoi ?

---

### Introduction
Aujourd'hui, c'est trolldi, alors je vous propose un petit article au long duquel nous allons tenter de réfléchir à ce qui fait ou non un "bon" langage de programmation.
À cet effet, je n'ai pas de définition à vous proposer concernant le terme "bon" : nous allons donc, de prime abord, tenter de rassembler les différentes conditions à réunir.

---

### Un "bon" langage, ça veut dire quoi ?
Honnêtement, il m'est impossible de mettre une définition sur ce mot.
Néanmoins, quelques questions peuvent nous donner une indication générale.
Je tiens à préciser que ces questions sont en relation avec *mes* critères, purement personnels, qui ne concernent et n'engagent que moi.
Vous êtes donc libres d'ignorer certains d'entre eux ou d'en rajouter d'autres !

Ces questions étant génériques, elles n'abordent qu'en surface les différents langages.

- À quel(s) domaine(s) d'application s'applique tel ou tel langage ?
  - Peut-on utiliser un même code dans deux applications différentes ?
  - Au contraire, est-il utile dans un et un seul cadre ? 
- Supporte-t-il la programmation générique ?
  - Ou doit-on réécrire une version de chaque classe / fonction pour chaque type désiré ?
- Est-il statiquement ou dynamiquement typé ?
  - Un langage statiquement typé sera plus sûr et plus facile à déboguer ;
  - Un langage dynamiquement typé demandera quant à lui davantage de rigueur.
- Est-il fortement ou faiblement typé ?
  - À titre d'exemple, C est faiblement typé car il permet des conversions implicites (`float` en `int`, `void*` en `int*`, ...). Elm est quant à lui très fortement typé car il est par exemple impossible d'additionner un `Int` et un `Float` sans conversion explicite.
  - Un langage très faiblement typé autorisera, par exemple, une comparaison entre les valeurs `1` et `"1"`.
- Est-il compilé (ou *transcompilé*) ou interprété ?
  - Un langage compilé ou transcompilé mettra en évidence certaines erreurs (notamment des erreurs de types) dès la compilation.
  - Un langage compilé proposera la plupart du temps de meilleures performances qu'un langage interprété.
  - Un langage compilé consommera généralement moins de mémoire qu'un langage interprété.
- Comment gère-t-il les erreurs ?
  - Passe-t-il par une valeur de retour, à la manière du type `error` en Go ou `Result<T, Error>` en Rust ?
  - Au contraire, doit-on prévoir de blinder le code de `try`/`catch` ?
- En bonus :
  - Est-il *null-safe* ? (présence ou absence d'une valeur `NULL`, `null`, `nil`, `undefined`, ...)
  - Peut-il respecter la *const-correctness* ?
  - Est-il facile à (re)lire ?

Ces questions abordent divers aspects de la programmation, qu'il s'agisse de sûreté (typage statique, fort, valeur nulle et gestion d'erreurs), performances (compilation), tout en abordant les domaines d'application et la maintenabilité.

La question du nombre d'utilisateurs n'est en revanche pas du tout pertinente : en effet, un langage en situation de monopole sera forcément très utilisé dans son domaine, ce qui fausserait les résultats !

---

### Cas d'école : JavaScript (presque au hasard)
Pour les besoins de cet article, j'ai choisi (presque au hasard) le langage JavaScript.
Incontournable dans le domaine du web, JS s'est imposé en tant que langage par défaut pour le développement côté client.
Il a ensuite été décliné en plusieurs variantes, comme TypeScript, son équivalent ~~presque~~ typé.

À présent, tentons d'établir une balance "pour / contre" au travers des questions vues précédemment :
- À quel(s) domaine(s) d'application s'applique JS ?
  - [x] : JS s'applique aussi bien au développement front qu'au back, par le biais de Node.js par exemple ! Il est par ailleurs possible de partager du code entre le front et le back, c'est un bon point !

- JS supporte-t-il la programmation générique ?
  - [x] Oui... ou presque. JS ne supporte théoriquement pas la programmation générique, mais il est possible de profiter de son système de type très faible pour simuler un comportement similaire.
- JS est-il statiquement ou dynamiquement typé ?
  - [ ] JavaScript est malheureusement dynamiquement typé. Cela induit qu'il faut faire preuve d'une certaine rigueur, car le langage n'avertit aucunement de l'assignation d'une valeur d'un type X à une variable originellement d'un type Y.
- JS est-il fortement ou faiblement typé ?
  - [ ] JS est (très) faiblement typé : il est donc possible de comparer des choux et des carottes. Rajoutons à cela qu'il est dynamiquement et implicitement typé et vous obtenez approximativement le pire système de type possible. Certes, il existe l'opérateur `===`, mais cela n'enlève rien au fait que le langage *permet* de faire n'importe quoi.
- JS est-il compilé ou interprété ?
  - [ ] JS est un langage interprété. Pour le développement front, ce n'est à mon sens pas une lacune. En revanche, côté back (Node.js), la concurrence est rude. Go, par exemple, offre de bien meilleures performances pour écrire un serveur HTTP, tout en ayant une emprunte mémoire bien plus légère. Par ailleurs, l'utilisation d'une variable non déclarée lèvera une exception au *runtime*, là où elle serait détectée dès la compilation dans d'autres langages.
- Comment JS gère-t-il les erreurs ?
  - [ ] JS utilise un système d'exceptions (oui, c'est un point négatif à mes yeux). Ce système rompt le flux d'exécution du programme et est donc susceptible de le faire arrêter net si l'exception n'est pas rattrapée. Une exception remontant chaque couche appelante jusqu'à être rattrapée, son origine peut par ailleurs être difficile à débusquer.
A contrario, les langages modernes passent pour la plupart par des valeurs de retour et des types adéquats (`error`, `Result<T, Error>`, ...). Ainsi, **la possibilité qu'une erreur survienne est mise en évidence dès l'appel d'une fonction/méthode** via son ou ses types de retour, et cette dernière peut alors être rattrapée aussi tôt que possible.

Questions bonus :
- Est-il *null-safe* ? (présence ou absence d'une valeur `NULL`, `null`, `nil`, `undefined`, ...)
  - [ ] JS ne propose pas une mais bien deux valeurs nulles, et permet même d'employer une variable non déclarée ; ce faisant, le risque d'employer à tort et sans le savoir une valeur nulle est accru !
- Peut-il respecter la const-correctness ?
  - [x] Oui ! Contrairement à beaucoup (trop) de langages, JS propose le mot-clé `const`, qui peut par ailleurs s'appliquer à *n'importe quelle variable*, pas seulement aux constantes de compilation ! Ce qui, en y réfléchissant, est logique pour un langage non compilé.
- Est-il facile à (re)lire ?
  - [x] Quoi que l'on puisse en dire, JS est, de par sa syntaxe très simple, facile à relire, ce qui en facilite la maintenabilité.

