## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) HS #1 - Création d'un outil de suivi de dépenses

---

### Introduction

D'ordinaire, je ne présente pas de projet perso ici. Néanmoins, je fais aujourd'hui une petite exception et en profite pour inaugurer la série "HS".
Il ne s'agit donc pas d'un article classique où je présente une techno ou fais part d'avis/conseils, mais plutôt un bilan, un ressenti général sur un petit projet que je mène sur mon temps libre. Par ailleurs, j'emploierai un ton un peu plus léger que d'habitude, à la manière d'un article "EXPRESS".

---

### Contexte

<p style="text-align:center;"><img src="images/contexte.png" width="350"  height="240" /></p>

Prenons notre DeLorean et envolons-nous en l'an de grâce 2017.

En 2017 (plus exactement en février, d'après les archives que j'ai retrouvées sur Discord), alors que je suis encore étudiant, je commence le développement d'un outil permettant de suivre et catégoriser ses dépenses.
Il s'agit alors d'une petite application sans prétention, faite en C#. Côté charte graphique, il a été décidé de partir sur un visuel rappelant les vieilles tirelires en forme de cochon. Je ne sais plus pourquoi, mais ça rendait bien. La direction artistique était alors toute trouvée : l'icône, les couleurs, tout était évident à partir du moment où l'idée de la tirelire a émergé. Même le nom du projet, Piggy, vient de l'anglais "piggy bank".

<p style="text-align:center;font-style: italic;"><img src="images/piggy_2017.png" /><br />Screen de l'application datant de 2017. C'est flashy, mais ça marche, c'est déjà ça !</p>

Cette petite application était essentiellement utilisée par mes proches, pour leurs besoins personnels. C'est encore le cas aujourd'hui, et de nouveaux besoins émergent :
* *Je veux pouvoir créer de nouvelles catégories ! Sinon, dans quoi je range les dépenses pour le chat ?*
* *Je veux pouvoir exporter mon bilan du mois vers Excel !*
* *Je veux des graphiques par mois/année/catégories !*
* *Je veux rechercher une dépense !*
* *Je veux [insérez ce que vous voulez ici] !*

Sauf que... mon PC d'étudiant a rendu l'âme, emportant avec lui les sources du projet, que je n'avais bien évidemment pas hébergées sur un repo distant. Quel génie.
Honnêtement, je dois reconnaître que ça m'a pas mal découragé. J'ai clairement laissé tombé le projet pendant plusieurs années, malgré plusieurs tentatives de le reprendre from scratch.

---

### 2020 : la résurrection du cochon

Retour en 2020, le présent à l'heure où je rédige ces lignes, probablement le passé pour vous. Nous sommes alors en décembre, et tout le monde attend la venue du Père Noël.

Face à de nouvelles demandes et profitant de mon temps libre, je décide de travailler "vite fait" sur un prototype d'une espèce de Piggy nouvelle génération. Rien de bien fou, à ce moment-là c'est juste pour me faire la main sur Go. L'idée est alors de me "challenger" un peu sur mes compétences en Go et voir jusqu'où je peux les pousser. Très rapidement, je m'oriente vers du web afin de pouvoir pleinement exploiter la puissance de Go (et pas du tout parce que le langage n'est pas taillé pour des applis de bureau ^^"). J'arrive donc à la conclusion suivante :
* Le back sera fait en Go ;
* Le front sera fait en HTML/CSS/JS (je reviendrai sur ce point plus bas dans l'article).

Cela revêt plusieurs avantages, notamment de pouvoir héberger le serveur sur mon poste perso ou sur la Rapberry Pi de la maison ; ainsi, je peux mettre à jour l'application sans déranger les utilisateurs/trices, tout se fait de manière transparente. Mais je reviendrai sur la partie purement technique dans une autre section.

Alors, en avant ! Je commence à écrire sur papier les différentes fonctionnalités à implémenter, réfléchir à leur implémentation, et développer un petit prototype. Heureux, j'arrive très rapidement (entre une demi journée et une journée) à un résultat !

<p style="text-align:center;font-style: italic;"><img src="images/piggy_2020.png" /><br />Screen de l'application, décembre 2020 (WIP).</p>

La plupart des fonctionnalités sont opérationnelles : on peut créer/modifier/supprimer des opérations, télécharger un rapport Excel, et le visuel est plus propre qu'avant.
