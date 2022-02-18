#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) HS #1 - Refonte d'un vieux projet from scratch
(Ou quand un dev back essaie de faire du front)

---

### Introduction

D'ordinaire, je ne présente pas de projet perso ici. Néanmoins, je fais aujourd'hui une petite exception et en profite pour inaugurer la série "HS".
Il ne s'agit donc pas d'un article classique où je présente une techno ou autre, mais plutôt d'un bilan, d'un ressenti général sur un petit projet que je mène sur mon temps libre. Par ailleurs, j'emploierai ici un ton un peu plus léger que d'habitude.

Spoiler : le projet en question.
<p style="text-align:center;font-style: italic;"><img src="images/piggy_2020_2.png" /><br />Screen de l'application, mi décembre.</p>

Il s'agit d'un petit outil visant à suivre ses dépenses, et qui propose quelques fonctionnalités sympa telles que des bilans annuels, des catégories, ou encore des exports vers Excel.
Mais avant de passer aux présentations, un peu d'histoire.

---

### Contexte

<p style="text-align:center;"><img src="images/contexte.png" width="350"  height="240" /></p>

Prenons notre DeLorean et envolons-nous en l'an de grâce 2017.

En 2017 (plus exactement en février, si je me fie aux archives que j'ai retrouvées sur Discord), alors que je suis encore étudiant, je commence le développement d'un logiciel permettant de suivre et catégoriser ses dépenses.
Il s'agit alors d'une petite application sans prétention, faite en C# (Winforms). Côté charte graphique, il a été décidé de partir sur un visuel rappelant les vieilles tirelires en forme de cochon. Je ne sais plus pourquoi, mais ça rendait bien. L'orientation visuelle générale était alors toute trouvée : l'icône, les couleurs, tout était évident à partir du moment où l'idée de la tirelire a émergé. Même le nom du projet, Piggy, vient de l'anglais "piggy bank".

<p style="text-align:center;font-style: italic;"><img src="images/piggy_2017.png" /><br />Screen de l'application datant de 2017. C'est flashy, mais ça marche, c'est déjà ça !</p>

Cette petite application était essentiellement utilisée par mes proches, pour leurs besoins personnels. C'est encore le cas aujourd'hui, et de nouveaux besoins émergent :
* *Je veux pouvoir créer de nouvelles catégories ! Sinon, dans quoi je range les dépenses pour le chat ?*
* *Je veux pouvoir exporter mon bilan du mois vers Excel !*
* *Je veux des graphiques par mois/année/catégories !*
* *Je veux pouvoir rechercher une dépense !*
* *Je veux [insérez ce que vous voulez ici] !*

Sauf que... mon PC d'étudiant a rendu l'âme, emportant avec lui les sources du projet, que je n'avais bien évidemment pas hébergées sur un repo distant. Quel génie.
Honnêtement, je dois reconnaître que ça m'a pas mal découragé. J'ai clairement laissé tomber le projet pendant plusieurs années, malgré plusieurs tentatives de le reprendre from scratch.

---

### 2020 : la résurrection du cochon

Retour en 2020, le présent à l'heure où je rédige ces lignes, probablement le passé pour vous. Nous sommes alors en décembre, et tout le monde attend la venue du Père Noël.

Face à de nouvelles demandes et profitant de mon temps libre, je décide de travailler "vite fait" sur un prototype d'une espèce de Piggy nouvelle génération. Rien de bien fou, à ce moment-là c'est juste pour me faire la main sur Go. C'est un langage que j'utilise depuis environ deux ans pour de tout petits projets (notamment des bots Discord), et que je voulais mettre à profit dans d'autres contextes. L'idée est alors de me "challenger" un peu sur mes compétences en Go et voir ce que j'arrive à faire avec. 

<p style="text-align:center;font-style: italic;"><img src="images/gopher.png" /><br />Gopher, la mascotte du langage.</p>

Très rapidement, je m'oriente vers du web afin de pouvoir pleinement exploiter la puissance de Go (et pas du tout parce que le langage n'est pas taillé pour des applis de bureau ^^"). J'arrive donc à la conclusion suivante :
* Le back sera fait en Go ;
* Le front sera fait en HTML/CSS/JS (je reviendrai sur ce choix plus bas dans l'article).

Cela revêt plusieurs avantages, notamment de pouvoir héberger le serveur sur mon poste perso ou sur ma Rapberry Pi ; ainsi, je peux mettre à jour l'application sans déranger les utilisateurs/trices, tout se fait de manière transparente. Pour les utilisateurs tiers, il suffira de télécharger le serveur et de l'ajouter à la liste des programmes se lançant au démarrage, puis d'ajouter l'url qui va bien à ses favoris ! (Bon, pour la mise à jour auto, ça sera en revanche un poil plus compliqué)

Alors, en avant ! Je commence à écrire sur papier les différentes fonctionnalités à intégrer, réfléchir à leur implémentation, et développer un petit prototype. Heureux, j'arrive très rapidement (entre une demi journée et une journée) à un premier résultat presque fonctionnel !

<p style="text-align:center;font-style: italic;"><img src="images/piggy_2020.png" /><br />Screen de l'application, début décembre 2020 (WIP).</p>

La plupart des fonctionnalités sont opérationnelles ! On peut pour le moment :
* Créer/modifier/supprimer des opérations ;
* Filtrer par catégorie/description (la liste se met à jour en temps réel lorsque l'on tape dans la barre de recherche) ;
* Trier le tableau en cliquant sur les entêtes de colonnes ;
* Voyager d'un mois à l'autre ;
* Télécharger un rapport Excel de l'année en cours, à raison d'une feuille par mois.

Enfin, le visuel est plus propre qu'avant. Le reste à faire est encore conséquent, mais ça avance !

Cerise sur le gâteau, le projet est hébergé sur un repo Git, et open source ([lien](https://github.com/vpenando/piggy)) ! Il est donc disponible pour tout le monde !

---

### Côté technique

Avant toute chose, je tiens à clarifier un point important : je suis loin d'être une brute en dev. C'est une discipline que j'apprécie énormément depuis plusieurs années, mais je ne sais bien évidemment pas tout faire ni forcément de la meilleure manière qui soit. Le code l'application n'est donc pas nécessairement exemplaire, mais a le mérite d'être simple, aussi bien à lire qu'à maintenir.

Comme brièvement évoqué plus haut, l'un des objectifs premiers de ce projet était de m'améliorer en Go. En effet, il s'agit d'un langage que j'affectionne énormément, et sur lequel je cherche à monter en compétence. Ainsi, ce petit projet, aussi mince soit-il, a été l'occasion d'utiliser pour la première fois des bibliothèques telles que [`gorilla/mux`](https://github.com/gorilla/mux) et [`gorm`](https://gorm.io/), par exemple.

Côté front, c'était en revanche un poil plus compliqué. Je suis essentiellement un dev orienté back et applicatif. La dernière fois où j'ai fait du front, c'est presque trop vieux pour que je m'en rappelle (aux alentours de 2012 ?). J'ai donc des notions très rudimentaires, tout juste niveau débutant.
De plus, j'ai notamment pour contrainte de n'avoir aucune dépendance tierce à faire installer aux utilisateurs, problème qui ne se pose pas en Go, où l'exécutable compilé n'a *aucune* dépendance. En effet, je veux que le projet soit utilisable tel quel lorsqu'un utilisateur *-notamment un utilisateur sans aucune connaissance technique-* le télécharge. On télécharge l'application, on la lance et ça marche. Or, en Go, l'exécutable final embarque toutes ses dépendances ; ça fait des binaires plus lourds, mais ils n'ont besoin de rien pour fonctionner.
Là où une application réellement orientée SaaS ne s'impose, par essence, pas de telles contraintes, je me dois de maintenir une certaine accessibilité auprès de chacun. Ainsi, je pars sur du JS natif avec quelques rustines persos histoire de me faciliter la tâche.

---

### FAQ

Dans cette section, je vais tâcher de répondre par avance à la plupart des interrogations que je m'attends à rencontrer :)

#### Quel intérêt par rapport à l'application fournie par ma banque ?
Toutes les banques ne fournissent pas les mêmes applications, mais elles ont pour la plupart des défauts communs. À titre d'exemple, ma banque (que je ne citerai bien évidemment pas), ne permet pas de rechercher simplement une opération. Il y a tout un formulaire (très lent à charger) à remplir avant de lancer la recherche. Par ailleurs, si vous avez par exemple un compte commun et un compte courant perso, cela vous oblige à potentiellement effectuer deux recherches pour retrouver une dépense.
De plus, je ne connais aucune application qui permet de classifier et ajouter une description personnalisée à une dépense (ou à un gain). Enfin, Piggy est amené à évoluer au fil des différentes demandes des utilisateurs, le rendant théoriquement de plus en plus viable au fil du temps.
Quoi qu'il en soit, c'est avant tout votre besoin qui détermine si telle ou telle application y est ou non adaptée.

#### Pourquoi un tel attrait pour Go ?
Cette question est tout à fait légitime : il existe une pléthore de langages de programmation, et chacun a ses domaines d'applications, alors pourquoi choisir Go ? Difficile de trouver un langage alliant expressivité, performances, stabilité et outils intégrés. Par exemple, C++ propose d'excellentes performances, mais est très lourd syntaxiquement, n'a pas d'outil de tests ou de documentation intégré, et certaines erreurs sont parfois difficiles à déceler.

Go offre la simplicité d'un langage de script, tout en proposant un système de types robuste et les performances d'un langage compilé (logique pour un langage compilé, me direz-vous). Par ailleurs, il fournit nativement [un environnement de test](https://vpenando.github.io/articles/fr/2020/go_test.html) et [un moteur de génération de documentation](https://vpenando.github.io/articles/fr/2020/express2_godoc.html). De plus, Go propose de nombreux outils tels que le fameux système de *goroutines*, et une bibliothèque standard très complète. Enfin, le binaire compilé n'a besoin d'aucune dépendance tierce, ce qui est un atout de choix pour un projet tel que celui-ci, voué à être installé sur un poste utilisateur (et sous n'importe quel OS).

#### Outils utilisés
Pour l'écriture du code, j'utilise [Visual Studio Code](https://code.visualstudio.com/). C'est un très bon outil qui offre de quoi écrire et déboguer son code très simplement. Pour vraiment faire le café, j'y ai ajouté quelques extensions, notamment l'extension Go créée par Google, qui permet entre autres de voyager dans le code via Ctrl+clic ou F12, comme le propose Visual Studio. Pour déboguer le code du front, j'utilise les [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools/). Pour consulter et modifier le contenu de la base de données, j'utilise [DB Browser for SQLite](https://sqlitebrowser.org/). Côté tests, j'utilise le package [`assert`](https://godoc.org/github.com/stretchr/testify/assert) en complément du package de tests standard de Go, ainsi que [Postman](https://www.postman.com/) pour les différentes requêtes. Enfin, pour la CI, j'utilise [Travis CI](https://travis-ci.org/).

#### L'application est-elle configurable ?
Partiellement. Il y a un fichier `config.ini` qui permet de modifier certaines choses. À l'heure où j'écris ces lignes, on peut modifier le port, la base de données et la langue du projet (actuellement français ou anglais). À terme, il y aura je l'espère plus d'éléments paramétrables, ainsi qu'une section dédiée dans l'application, plus user friendly !

#### Où sont stockées les diverses données de l'utilisateur ?
L'ancienne application stockait les données dans de bêtes fichiers CSV, à raison d'un fichier par mois (ça faisait les rapports Excel tout seul comme ça :D). Aujourd'hui, cette nouvelle version utilise une vraie base de donnée, ici SQLite. N'ayant nul besoin de la protéger par un login/mot de passe, j'ai opté pour la solution la plus simple. Il serait néanmoins très simple de modifier le code de façon à faire pointer le serveur vers une base Postgre, MySQL/MariaDB ou autre.

#### Difficultés rencontrées
Si la partie serveur a été très simple à développer, le côté front m'a demandé un peu plus de travail, notamment pour me remettre dans le bain. J'ai néanmoins, au cours de mes recherches préliminaires, pu tester TypeScript et Elm, que j'ai beaucoup appréciés. Je les utiliserai sûrement pour de prochains projets. C'est assez frustrant de faire du JS après avoir goûté à TypeScript. Par ailleurs, j'ai eu quelques soucis avec l'extension Go de VSCode (un petit bug de rien du tout qui dupliquait mes lignes :D), mais c'est un outil open source alors c'est pardonné. J'en profite pour remercier l'équipe du plugin (notamment Rebecca Stambler de chez Google qui m'a aidé à régler le soucis, même si elle ne lira probablement jamais cet article).
Par ailleurs, c'est un détail, mais la création de nouvelles catégories (plus spécifiquement l'upload de l'icône) m'a fait essuyer pas mal de plâtre.
Enfin, étant habitué à C++ et C# (surtout C#, que j'utilise dans ma vie professionnelle), certains aspects de la POO m'ont pas mal manqué.

#### Quel intérêt par rapport à un bon vieux document Excel ?
Avant tout, il ne s'agit aucunement d'un comparatif dans un cadre pro (et je doute sincèrement de la pertinence de Piggy dans un contexte professionnel). Néanmoins, pour mes proches (soit les utilisateurs principaux :D), Piggy revêt plusieurs intérêts, à commencer par sa simplicité et sa clarté. En effet, la prise en main de la bête est quasi immédiate sitôt l'application ouverte. Aucune fonctionnalité n'est compliquée à appréhender, et le visuel est extrêmement simple à interpréter, notamment grâce aux catégories.

---

### Fonctionnalités à venir
Certaines fonctionnalités majeures m'ont d'ores et déjà été demandées. Parmi elles, les plus attendues sont :
* Création / édition unitaire depuis la page d'accueil (sans besoin de passer par la page d'édition puis rechercher l'occurence à modifier) ;
* Ajout d'une page de bilan par catégorie sur un mois / une année ;
* Ajout de graphiques pour avoir une comparaison visuelle entre chaque mois ou catégorie.

En parallèle, d'autres fonctionnalités, moindres, sont également en attente :
* Mise en évidence et déplacement du bouton "Edition" pour éviter les "missclicks" ;
* Mise à disposition d'un jeu d'icônes "toutes faites" lors de la création de nouvelles catégories.

Ainsi, bien que le prototype soit (presque) pleinement fonctionnel, il reste encore un peu de travail pour arriver à un résultat final complet. Toutefois, travaillant seul sur le projet et uniquement sur mon temps libre, il est probable que les nouvelles fonctionnalités mettent un certain temps avant de faire leur apparition.

---

### Code source du projet
Le repo Git du projet est disponible [ici](https://github.com/vpenando/piggy).
Il contient les dossiers suivants :
* `categories/` : là où sont stockées les images de chaque catégorie, avec des images "par défaut" pour vos catégories ;
* `css/` : les fichiers CSS du projet ;
* `images/` : les images "fixes" du projet : icône, boutons, ... ;
* `piggy/` : le package (au sens Go) principal du projet, où sont les principales fonctionnalités du projet (\*) ainsi que leurs tests ;
* `reports/` : là où sont stockés les rapports Excel avant export ;
* `scripts/` : les scripts JS du projet ;
* `test/` : les tests tiers du projet (stresstests, ...) ;
* `views/` : les pages HTML (templates mux) du projet.

**\*** L'intérêt d'en faire un package à part entière permet une meilleure réusabilité du cœur de l'appli pour des projets futurs. 

**NOTE DU FUTUR (01/2022)** : L'architecture du projet a été remaniée afin d'être davantage "go compliant".
