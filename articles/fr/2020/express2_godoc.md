## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) EXPRESS #2 - Du bon usage de `godoc`

###### Note importante
> Cet article fait partie de la série "EXPRESS". Il s'agit d'une série d'articles relativement courts et moins formels, axés autour d'un point sur lequel je ne me voyais pas écrire un article complet. Le ton y est volontairement plus léger afin d'en faciliter la lecture (et l'écriture !).

---

Bonjour !

Si vous êtes ici, c'est que vous avez lu la note en début de page (a priori juste au-dessus), et que vous ne vous attendez par conséquent pas à un article très formel comme ce que j'ai l'habitude de publier sur ce blog.

Au court de ce bref article, je vais présenter l'outil de génération de doc inclus avec Go, et comment l'utiliser. Rien de bien sorcier, vous verrez !

<img src="images/gopher.png" style="align:center" />
<br />
<label style="text-align:center" >aaa</label>

---

### La documentation à l'ère du jurassique
Il y a fort longtemps, lorsque la terre n'abritait pas encore l'humanité, écrire une doc était très fastidieux. Déjà parce que les dinosaures ne savaient pas écrire, mais surtout parce que l'on n'avait pas d'outils vraiment taillés pour.
Puis virent le jour des outils tels que [Doxygen](http://www.doxygen.nl/), permettant de générer une documentation à partir des commentaires contenus dans vos fichiers source.
