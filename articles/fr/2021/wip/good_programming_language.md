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
  - Un langage dynamiquement type demandera quant à lui davantage de rigueur.
- Est-il fortement ou faiblement typé ?
  - Un langage faiblement typé autorisera, par exemple, une comparaison entre les valeurs `1` et `"1"`.
- Est-il compilé ou interprété ?
  - Certaines erreurs (notamment des erreurs de types) peuvent être détectées dès la compilation.
  - Un langage compilé proposera bien souvent des meilleures performances qu'un langage interprété.
  - Un langage compilé consommera généralement moins de mémoire qu'un langage interprété.
- Comment gère-t-il les erreurs ?
  - Passe-t-il par une valeur de retour ?
  - Au contraire, doit-on prévoir de blinder le code de `try`/`catch` ?
- En bonus :
  - Propose-t-il une valeur nulle ? (`NULL`, `null`, `nil`, `undefined`, ...)
  - Peut-il respecter la *const-correctness* ?
  - Est-il facile à (re)lire ?
  - Qu'en est-il du *tooling* ?

La question du nombre d'utilisateurs n'est en revanche pas du tout pertinente : en effet, un langage en situation de monopole sera forcément très utilisé dans son domaine, ce qui fausserait les résultats !

---

### Cas d'école : JavaScript (presque au hasard)
Pour les besoins de cet article, j'ai choisi (presque au hasard) le langage JavaScript.
Incontournable dans le domaine du web, JS s'est imposé en tant que langage par défaut pour le développement côté client.
Il a ensuite été décliné en plusieurs variantes, comme TypeScript, son équivalent ~~presque~~ typé.

À présent, tentons d'établir une balance "pour / contre" au travers des question vue précédemment :
- À quel(s) domaine(s) d'application s'applique JS ?
  - [x] JS s'applique aussi bien au développement front qu'au back par le biais de Node.js. Il est par ailleurs possible de partager du code entre le front et le back, c'est un bon point !
- JS Supporte-t-il la programmation générique ?
  - [ ] Oui... mais non. JS ne supporte théoriquement pas la programmation générique, mais il est possible de profiter de son système de type très faible pour simuler un comportement similaire.
- JS est-il statiquement ou dynamiquement typé ?
  - [ ] JavaScript est malheureusement dynamiquement typé. Cela induit qu'il faut faire preuve d'une certaine rigueur, car le langage n'avertit aucunement d'une mauvaise assignation à une mauvaise variable.
- JS est-il fortement ou faiblement typé ?
  - [ ] JS est (très) faiblement typé : il est donc possible de comparer des choux et des carottes. Rajoutons à cela qu'il est dynamiquement et implicitement typé et vous obtenez approximativement le pire système de type possible.
- JS est-il compilé ou interprété ?
  - [ ] JS est un langage interprété. Pour le développement front, ce n'est à mon sens pas une lacune. En revanche, côté back (Node.js), la concurrence est rude. Go, par exemple, offre de bien meilleures performances pour écrire un serveur HTTP, tout en ayant une emprunte mémoire bien plus légère.
- Comment JS gère-t-il les erreurs ?
  - [x] a
