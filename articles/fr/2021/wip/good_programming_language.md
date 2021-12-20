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

- À quel(s) domaine(s) d'application s'applique tel ou tel langage ?
  - Peut-on utiliser un même code dans deux applications différentes ?
  - Au contraire, est-il utile dans un et un seul cadre ? 
- Supporte-t-il la programmation générique ?
  - Ou doit-on réécrire une version de chaque classe / fonction pour chaque type désiré ?
- Est-il statiquement ou dynamiquement typé ?
  - Un langage statiquement typé sera beaucoup plus facile à déboguer ;
  - Un langage dynamiquement typé demandera quant à lui davantage de rigueur.
- Est-il fortement ou faiblement typé ?
  - À titre d'exemple, C est faiblement typé car il permet implicitement de convertir `void*` en `int*`.
  - Un langage très faiblement typé autorisera, par exemple, une comparaison entre les valeurs `1` et `"1"`.
- Est-il compilé (ou *transcompilé*) ou interprété ?
  - Certaines erreurs (notamment des erreurs de types) peuvent être détectées dès la compilation.
  - Un langage compilé proposera bien souvent de meilleures performances qu'un langage interprété.
  - Un langage compilé consommera généralement moins de mémoire qu'un langage interprété.
- Comment gère-t-il les erreurs ?
  - Passe-t-il par une valeur de retour, à la manière du type `error` en Go ou `Result<T, Error>` en Rust ?
  - Au contraire, doit-on prévoir de blinder le code de `try`/`catch` ?
- En bonus :
  - Propose-t-il une valeur nulle ? (`NULL`, `null`, `nil`, `undefined`, ...)
  - Peut-il respecter la *const-correctness* ?
  - Est-il facile à (re)lire ?

Ces questions abordent divers aspects de la programmation, qu'il s'agisse de sûreté (typage statique, fort, valeur nulle et gestion d'erreurs), performances (compilation), tout en abordant les domaines d'application et la maintenabilité.

La question du nombre d'utilisateurs n'est en revanche pas du tout pertinente : en effet, un langage en situation de monopole sera forcément très utilisé dans son domaine, ce qui fausserait les résultats !

---

### Cas d'école : JavaScript (presque au hasard)
Pour les besoins de cet article, j'ai choisi (presque au hasard) le langage JavaScript.
Incontournable dans le domaine du web, JS s'est imposé en tant que langage par défaut pour le développement côté client.
Il a ensuite été décliné en plusieurs variantes, comme TypeScript, son équivalent ~~presque~~ typé.

À présent, tentons d'établir une balance "pour / contre" au travers des question vue précédemment :
- À quel(s) domaine(s) d'application s'applique JS ?
  - [x] - JS s'applique aussi bien au développement front qu'au back, par le biais de Node.js par exemple ! Il est par ailleurs possible de partager du code entre le front et le back, c'est un bon point !
- JS supporte-t-il la programmation générique ?
  - [x]   Oui... ou presque. JS ne supporte théoriquement pas la programmation générique, mais il est possible de profiter de son système de type très faible pour simuler un comportement similaire.
- JS est-il statiquement ou dynamiquement typé ?
  - [ ] JavaScript est malheureusement dynamiquement typé. Cela induit qu'il faut faire preuve d'une certaine rigueur, car le langage n'avertit aucunement d'une mauvaise assignation à la mauvaise variable.
- JS est-il fortement ou faiblement typé ?
  - [ ] JS est (très) faiblement typé : il est donc possible de comparer des choux et des carottes. Rajoutons à cela qu'il est dynamiquement et implicitement typé et vous obtenez approximativement le pire système de type possible.
- JS est-il compilé ou interprété ?
  - [ ] JS est un langage interprété. Pour le développement front, ce n'est à mon sens pas une lacune. En revanche, côté back (Node.js), la concurrence est rude. Go, par exemple, offre de bien meilleures performances pour écrire un serveur HTTP, tout en ayant une emprunte mémoire bien plus légère. Par ailleurs, l'utilisation d'une variable non déclarée lèvera une exception au *runtime*, là où elle serait détectée dès la compilation dans d'autres langages.
- Comment JS gère-t-il les erreurs ?
  - [ ] JS utilise un système d'exceptions (Oui c'est un point négatif à mes yeux). Par nature, ce système rompt le flux d'exécution du programme et donc susceptible de le faire arrêter net si l'exception n'est pas rattrapée. A contrario, les langages modernes passent pour la plupart par des valeurs de retour et des types adéquats (`error`, `Result<T, Error>`, ...). Ainsi, il est beaucoup moins probable que le programme plante suite à une erreur.

Questions bonus :
- Propose-t-il une valeur nulle ? (`NULL`, `null`, `nil`, `undefined`, ...)
  - [ ] JS ne propose pas une mais bien deux valeurs nulles ! Ce faisant, le risque d'employer à tort une valeur nulle est accru !
- Peut-il respecter la const-correctness ?
  - [x] Oui ! Contrairement à beaucoup (trop) de langages, JS propose le mot-clé `const`, qui peut par ailleurs s'appliquer à *n'importe quelle variable*, pas seulement aux constantes de compilation ! Ce qui, en y réfléchissant, est logique pour un langage non compilé.
- Est-il facile à (re)lire ?
  - [x] Quoi que l'on puisse en dire, JS est, de par sa syntaxe très simple, facile à relire, ce qui en facilite la maintenabilité.

