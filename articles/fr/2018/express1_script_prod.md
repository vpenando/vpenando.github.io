## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) EXPRESS #1 - Comment ne pas casser la prod

###### Note importante
> Cet article est le premier de la lignée "EXPRESS". Il s'agit d'une série d'articles relativement courts et moins formels, axés autour d'un point sur lequel je ne me voyais pas écrire un article complet. Le ton y est volontairement plus léger afin d'en faciliter la lecture (et l'écriture !).

---

Bonjour !

Si vous êtes ici, c'est que vous avez lu la note en début de page (a priori juste au-dessus), et que vous ne vous attendez par conséquent pas à un article très formel comme ce que j'ai l'habitude de publier sur ce blog.
Aujourd'hui, je ne vais pas pinailler sur de la sémantique, un paradigme ou quelque réflexion que ce soit.

Non, je vais plutôt vous présenter une petite astuce toute bête qui m'évite de casser la prod avant d'avoir ingéré mon litre de caféïne quotidien.

Il y a un petit moment, j'ai laissé à un collègue l'accès à ma VM de test pour un exercice tout bête, qui consistait à ajouter et modifier des données dans une base de données.
Après avoir effectué ses tests, ledit collègue a eu la bonté de supprimer les données qu'il avait ajoutées... ou presque. En effet, à la suite d'une fausse manip, sa requête SQL est passée de `DELETE FROM <base> WHERE <condition>` à `DELETE FROM <base>`. Tout court. Sans condition.

Autant donc dire que l'on a dû restaurer la VM à un snapshot plus ancien afin de récupérer le contenu de la table en question. Rien de bien dramatique donc, mais imaginez qu'une telle erreur arrive en production...?

---

Cela m'a permis de réfléchir à une manière de limiter les erreurs en production ; comment éviter qu'une commande manifestement risquée ne dérape ? Ayant l'habitude d'interagir avec ma machine via des scripts, j'ai pris la décision d'y apporter certaines petites modifications afin de me prémunir de potentielles actions irréparables.

Dans l'idée, c'est tout bête : par défaut le script sera exécuté en mode "simulation", et aucune action d'écriture ne sera effectuée. Il faudra alors spécifier une option afin que le script fasse réellement les actions demandées.

Exemple :

```sh
#!/bin/bash

user=$(whoami)
userid=$(id -u)
mode=0

echo "Info: Starting script '$0'"
echo "Info: Started by '$user' ($userid)"
echo "Info: Running on host '$HOSTNAME'" 
echo

# Précondition 1...
# Précondition 2...
# On peut tester ici le nombre d'arguments, par exemple

# OK, les préconditions sont vérifiées !

# Par défaut 'for' itère sur les arguments du script.
# 'last' est donc le dernier argument.
for last; do true; done 
if [[ $last = "/process" ]]; then
  mode=1
fi

if [[ $mode -eq 0 ]]; then
  echo "Info: Working in simulation mode"
  echo "Info: Add '/process' as last argument to work in real mode"
  echo
fi

# À partir d'ici, je teste la valeur de 'mode' avant toute action d'écriture.
# Si 'mode' est différent de 1, j'affiche à la place 'Simulating XXX', où 'XXX' est la commande
# à exécuter.

if [[ $mode -eq 1 ]]; then
  # Admettons que je lance une requête du type "DELETE FROM $1 WHERE $2"
  echo "Running query DELETE FROM $1 WHERE $2..."
  # La (terrible) requête
  echo "Done"
else
  echo "Simulating query DELETE FROM $1 WHERE $2..."
  # On pourrait presque faire un SELECT count(*) FROM $1 where $2
  # pour afficher combien d'éléments seraient affectés.
fi
```

Exemple d'output type :
```
Info: Starting script 'exampleQuery.sh'
Info: Started by 'vincent' (123456)
Info: Running on host 'nulix'

Info: Working in simulation mode
Info: Add '/process' as last argument to work in real mode

Simulating query DELETE FROM sample WHERE 1=1...
```

Là, il ne reste qu'à se relire puis relancer le script avec l'option `/process`. Ainsi, on est sûr qu'en laissant ce script entre les mains d'un collègue insuffisamment caféïné, le risque de tout casser s'amoindrit.

On peut même assez facilement logger qui fait quoi si l'on prend la peine de rediriger l'output vers un fichier de log quelconque (`./exampleQuery.sh base 1=1 /process >> out.log`).

---

Ainsi s'achève ce premier (et bref) article de la série EXPRESS. J'espère que cette petite astuce permettra d'éviter quelques mauvaises manips qui auraient le mauvais goût de casser des choses critiques.

J'espère également que ce type de contenu vous plaira, bien que le sujet soit traité avec plus de légèreté que d'habitude.

