## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) À la découverte du langage Elm !

---

### Sommaire
* [Introduction](#introduction)
* [Pourquoi choisir Elm ?](#pourquoi-elm)
* [Présentation](#presentation)
  - [Variables](#variables)
  - [Fonctions](#fonctions)
  - [Application partielle de fonction](#application-partielle)
  - [Pattern matching](#pattern-matching)
  - [Définir ses propres types](#definir-types)
  - [Modules](#modules)
* [La gestion des erreurs](#gestion-erreurs)
* [Conclusion](#conclusion)

---

### <a name="introduction">Introduction</a>

Initialement apparu en 2012, Elm est un langage de programmation orienté front-end.
Il s'agit d'un langage fonctionnel, syntaxiquement très proche de Haskell, se veuillant sûr et performant. La promesse d'Elm est très ambitieuse : **AUCUNE erreur au runtime** !

Elm est un langage transpilé en JavaScript afin de s'intégrer dans une page HTML.

Dans cet article, nous n'aborderons pas Elm en profondeur ; nous verrons assez succinctement sa syntaxe et ses fonctionnalités.

Vous pouvez tester Elm dans votre navigateur [à cette adresse](https://elm-lang.org/try).

---

### <a name="pourquoi-elm">Pourquoi choisir Elm ?</a>

Personnellement, je ne suis pas un grand adepte de développement web. À vrai dire, cela est essentiellement dû au fait que je n'apprécie pas JS.
En effet, JavaScript est un langage ayant de nombreux problèmes, comme le typage dynamique, ses valeurs `null` et `undefined`, et j'en passe. 

Bien qu'il existe des alternatives (je pense notamment à TypeScript + Angular), celles-ci ne sont pas exemptes de faiblesses, notamment concernant [la fameuse erreur à un milliard](https://www.developpez.com/actu/195314/Retour-sur-une-proposition-pour-C-8-les-references-nullables-quels-sont-les-avantages-de-cette-fonctionnalite/).

Elm s'affranchit de la plupart des problèmes posés par JS ; il **impose** de la sûreté via divers mécanismes, à tel point qu'il est *impossible* de rencontrer des erreurs inattendues au runtime. La quasi totalité de celles-ci est détectée dès la compilation.

Par ailleurs, [Elm propose d'excellentes performances](https://www.freecodecamp.org/news/a-real-world-comparison-of-front-end-frameworks-with-benchmarks-e1cb62fd526c/), bien au-dessus de la plupart des solutions concurrentes.

<p style="text-align:center;"><img src="assets/elm.png" /></p>
<p style="text-align:center;"><a href="https://altenwald.org/2018/04/11/elm-codigo-sin-errores/">(Source)</a></p>

Enfin, il est possible d'utiliser conjointement Elm et JavaScript, facilitant son intégration au sein d'un projet existant.

---

### <a name="presentation">Présentation</a>

#### <a name="variables">Variables</a>
À la manière de Python ou Ruby, déclarer une variable en Elm est on ne peut plus simple :
```elm
hello = "Hello, world!"
```
Et c'est tout !
Cette variable est fortement typée et surtout, **elle est immutable** !
Une fois définie, il est **impossible de la modifier**. Cela peut sembler contraignant au premier abord, mais c'est en réalité un atout majeur.
En effet, une variable constante induit :
* Qu'aucun effet de bord ne peut la modifier ;
* Que cette variable est thread-safe ;
* Qu'elle est facile à déboguer : contrairement à d'autres langages, nul besoin de chercher où elle est modifiée !

Et surtout, toutes les variables étant immutables, nul besoin d'en créer des copies, ce qui induit des performances accrues !

Elm dispose des types primitifs "classiques" `Int`, `Float`, `Bool`, `Char` et `String` :
```elm
someInt = 42
someFloat = 3.14
someBool = True
someChar = 'A'
someString = "Hello, world!"
```
De plus, il existe des types tels que `Maybe a` ou `List a`, où `a` désigne un type quelconque. Le type `Maybe` désigne une valeur optionnelle et est l'équivalent de `Option` en OCaml ou Rust (entre autres), et est similaire au type éponyme existant en Haskell. Il s'agit de la meilleure réponse possible aux problèmes posés par des valeurs telles que `null` ou `undefined`.

`Maybe a` propose les variants `Just a` et `Nothing`, correspondant respectivement à la présence et l'absence de valeur :
```elm
a = Just 42  -- on a une valeur, ici 42
b = Nothing  -- pas de valeur !
```
***Note -** Si vous êtes étranger·ère à Haskell ou aux langages fonctionnels en général, considérez simplement le type `Maybe` comme une forme d' `enum` améliorée.*

#### <a name="fonctions">Fonctions</a>
En Elm, toute fonction est également une variable. Ainsi, déclarer une fonction se fait de la même manière, en y ajoutant les paramètres :
```elm
-- fonction faisant la somme de deux nombres x et y
add x y = x + y  -- pas besoin de mot-clé 'return'
```
Il existe également la notion de fonction anonyme (ou expression lambda) ; c'est très utile pour passer une fonction en argument sans devoir la déclarer préalablement ! Déclarer une fonction anonyme se fait de la manière suivante et n'est pas sans rappeler les *arrow functions* en JS ou C# :
```elm
anonymousFunc = \x -> x * 2
```
***Note -** Le caractère `\` est employé en référence au symbole lambda (λ), relativement au terme "expression lambda" !*

Pour déclarer des variables *à l'intérieur* d'une fonction, il faut utiliser les mots-clés `let` et `in` :
```elm
add x y =
  let first = x in
  let second = y in
  first + second
  
-- ou plus simplement :
add x y =
  let first = x
      second = y
  in first + second
```
Il est également possible *-et recommandé-* d'annoter une fonction de sa signature, comme suit :
```elm
add : Int -> Int -> Int
add x y = x + y

-- et à l'usage :
five = add 2 3 -- pas de parenthèses nécessaires !
```
L'annotation de `add` peut à première vue sembler confuse. En effet, cette fonction attend deux variables de types `Int` et renvoie un résultat, lui-même de type `Int`.
Ainsi, on pourrait de prime abord s'attendre à une signature proche de `(Int, Int) -> Int`. Néanmoins, en programmation fonctionnelle, `add` est une fonction attendant un argument et *renvoyant une autre fonction* attendant également un argument. Plus d'informations [ici](https://guide.elm-lang.org/appendix/function_types.html).
Vous l'aurez donc probablement compris, on peut profiter de l'*application partielle de fonction* en Elm !

#### <a name="application-partielle">Application partielle de fonction</a>
Conceptuellement, l'application partielle de fonction consiste à appeler une fonction avec seulement une partie de ses arguments.
En résulte une nouvelle fonction, qui attend le reste des arguments.
```elm
add : Int -> Int -> Int
add x y = x + y

add2 : Int -> Int
add2 x = add 2
```
En combinaison avec des fonctions telles que [`List.map`](https://package.elm-lang.org/packages/elm/core/latest/List#map) ou [`List.filter`](https://package.elm-lang.org/packages/elm/core/latest/List#filter), il devient alors aisé de créer des fonctions spécialisées et réutilisables !

#### <a name="pattern-matching">Pattern matching</a>
Le pattern matching est une sorte de `switch`/`case`, mais avec quelques stéroïdes en plus.
En effet, en plus de tester différentes valeurs possibles, il est possible de *déstructurer une variable*.
Par exemple, comment implémenter une fonction renvoyant la valeur contenue dans une variable de type `Maybe Int`, ou une valeur par défaut si cette variable vaut `Nothing` ?
Je vous le donne en mille, c'est très facile en utilisant le pattern matching !
```elm
valueOrDefault : Maybe Int -> Int -> Int
valueOrDefault maybe default =
  case maybe of
    Just x  -> x       -- il y a une valeur : on la renvoie !
    Nothing -> default -- pas de valeur : on renvoie celle par défaut
```
***Note -** Dans le cadre d'une vraie application, nous utiliserions plutôt [`Maybe.withDefault`](https://package.elm-lang.org/packages/elm/core/latest/Maybe#withDefault) pour couvrir ce genre de cas.*

On peut également l'utiliser pour décomposer une liste. À titre d'exemple, voici comment calculer la somme d'une liste d'entiers :
```elm
sumWithBase : Int -> List Int -> Int
sumWithBase base list =
  case list of
    []     -> base
    hd::tl -> sumWithBase (hd+base) tl
    
sum = sumWithBase 0 -- on utilise l'application partielle pour définir notre fonction 'sum'
```
Ici, on a décomposé notre liste en `hd`, son premier élément, et `tl`, qui correspond au reste ! Conventionnellement, son premier élément est appelé la tête (d'où le nom `hd`, pour "head") et le reste est appelé la queue (d'où le nom `tl`, pour "tail").

#### <a name="definir-types">Définir ses propres types</a>
En Elm, il existe trois façons de définir un type, en fonction des besoins.
Tout d'abord, il est possible de définir un alias de type existant :
```elm
type alias MyInt1 = Int -- MyInt1 == Int
type       MyInt2 = Int -- MyInt2 != Int
```
Ici, `MyInt1` est "juste" un alias du type `Int`. Il s'agit littéralement du *même* type.
Sans le mot-clé `alias`, on crée un nouveau type, distinct du type `Int`, comme le type `MyInt2`.

Ensuite, il existe les types sommes, à la manière du type `Maybe` vu plus haut.
À titre d'exemple, ce même type `Maybe` est implémenté ainsi :
```elm
type Maybe a
  = Just a
  | Nothing
```
On peut implémenter des énumérations classiques de cette manière :
```elm
type Switch
  = On
  | Off

-- l'implémentation du type Bool
type Bool
  = True
  | False
```
Les types sommes sont cependant bien plus riches que de bêtes énumérations ! Rappelez-vous du type `Maybe`, qui attend un type en paramètre et expose le variant `Just` permettant d'accéder à la valeur sous-jacente !
Il est possible d'implémenter un comportement similaire !
```elm
-- une donnée quelconque de type int, bool ou string
type DataType
  = Integer Int
  | Boolean Bool
  | Text    String
```
Bien entendu, ils peuvent également être utilisés conjointement avec le pattern matching !
```elm
type Operation a
  = Add a a
  | Sub a a
  | Mul a a
  | Div a a

-- et à l'usage :
applyOperation : Operation Int -> Int
applyOperation op =
  case op of
    Add x y -> x + y
    Sub x y -> x - y
    Mul x y -> x * y
    Div x y -> x // y  -- la division d'entiers s'effectue via l'opérateur //

add23 = Add 2 3  -- 'add23' est ici de type Operation Int
five = applyOperation add23

-- on peut évidemment complexifier les choses !
two = applyOperation (Div (applyOperation (Mul 5 2)) (applyOperation (Add 2 3)))
```
Enfin, il existe les "records", très similaires aux `struct`s dans d'autres langages. Par convention, on les déclare en tant que `type alias`. Tout "record" dispose d'un constructeur de type afin de faciliter la création de ses instances.
```elm
type alias User =
  { nickname : String
  , email : String
  }

myUser = User "Toto" "toto@toto.com"

-- et en prime, un record peut être générique !
type alias KeyValue a b =
  { key : a
  , value : b
  }

keyValue = KeyValue 0 "Zero"  -- KeyValue Int String
```

#### <a name="modules">Modules</a>

Les différentes fonctionnalités d'Elm sont réparties dans des *modules*. Nous avons évoqué plus haut les fonctions `List.map` et `List.filter`. Ces fonctions, relatives au type `List`, sont stockées dans le module du même nom ; pratique pour s'y retrouver ! Il en va de même pour les fonctions relatives au type `Maybe`, par exemple !

D'une manière générale, tout fichier source *est* un module. Pour importer un module en Elm, il suffit d'utiliser le mot-clé `import` :
```elm
import MyModule

-- et pour utiliser son contenu :
result = MyModule.myFunction
```
Si l'on veut directement importer une fonctionnalité d'un module dans le module courant (sans avoir besoin de préfixer ses appels par le nom du module), on utilise le mot-clé `exposing` :
```elm
import MyModule exposing (myFunction)
-- si l'on veut importer TOUT le contenu d'un module :
-- import MyModule exposing (..)

result = myFunction
```
Enfin, [certains modules sont directement importés en Elm](https://package.elm-lang.org/packages/elm/core/latest/), expliquant pourquoi certains types sont directement disponibles dans tout fichier source !

---

### <a name="gestion-erreurs">La gestion des erreurs</a>
Elm ne dispose pas de système d'exceptions. Par nature, une exception rompt le flux d'exécution du programme et, si elle n'est pas correctement rattrapée, peut le faire crasher.
Il s'agit d'un concept inacceptable en Elm ; à la place, tout cas d'erreur est *prévu* en amont. En effet, toute fonction susceptible d'échouer renverra un résultat en conséquence.
Prenons un exemple extrêmement simple :
```elm
list = []  -- une liste vide

-- List.head renvoie le premier élément d'une liste
head = List.head list -- ???
```
Dans cet exemple, que vaut `head` ? Rappelons-nous qu'il n'y a ni exceptions, ni valeur nulle en Elm.
Jetons un oeil à la [signature de `List.head`](https://package.elm-lang.org/packages/elm/core/latest/List#head) :
```elm
head : List a -> Maybe a
```
Nous constatons que cette fonction, susceptible d'échouer si la liste est vide, renvoie une variable de type `Maybe a`. Ainsi, il est aisé de déduire que notre variable `head` dans l'exemple ci-dessus vaut `Nothing`. Nous ne pouvons donc pas l'utiliser sans vérifier ce qu'elle vaut, par exemple via du pattern matching :
```elm
list = []
head = List.head list

case head of
  Just x  -> ... -- on peut utiliser le premier élément, ici 'x'
  Nothing -> ... -- aucune valeur
```
Mieux encore, nous pouvons utiliser la fonction [`Maybe.map`](https://package.elm-lang.org/packages/elm/core/latest/Maybe#map) ou [`Maybe.andThen`](https://package.elm-lang.org/packages/elm/core/latest/Maybe#andThen) sur notre variable `head` si nous voulons y appliquer une fonction :
```elm
list = []
head = List.head list

-- imaginons que nous voulons multiplier 'head' par 2 si cette valeur existe :
head2 = Maybe.map (\x -> x * 2) head
```
La fonction `Maybe.map` est très bien faite : si nous lui passons une variable valant `Just x`, alors elle renverra `Just (func x)`.
Sinon, si le dernier argument vaut `Nothing`, alors elle renvoie... `Nothing` ! Il n'y a donc aucun risque de plantage !

Par ailleurs, Elm propose le type `Result`, implémenté ainsi :
```elm
type Result error value
  = Ok value
  | Err error
```
Il s'agit d'un type somme proposant les variants `Ok` et `Err`, qui n'est pas sans rappeler [le type éponyme en Rust](https://doc.rust-lang.org/std/result/enum.Result.html) ! Ainsi, si une fonction est susceptible de renvoyer une erreur, alors nous utiliserons le type `Result`. Tout comme pour `Maybe`, il nous est imposé d'en vérifier le contenu, empêchant donc tout comportement inattendu !
Cerise sur le gâteau, il existe les fonctions [`Result.toMaybe`](https://package.elm-lang.org/packages/elm/core/latest/Result#toMaybe) et [`Result.fromMaybe`](https://package.elm-lang.org/packages/elm/core/latest/Result#fromMaybe) afin de transposer une variable d'un type à l'autre !

---

### <a name="conclusion">Conclusion</a>

Dans cet article, nous avons abordé Elm en surface. Nous avons vu sa syntaxe basique et une partie des mécanismes du langages, sans pour autant aborder l'architecture d'une application Elm. Ce point pourrait cependant faire l'objet d'un article futur, car il y a matière à traiter sur le sujet !

Ce billet avait avant tout pour but de vous présenter les multiples intérêt d'Elm, à commencer par sa sûreté, que l'on ne retrouve pas dans la plupart des autres langages, notamment dans le domaine du développement web. Si vous souhaitez approfondir vos connaissances sur le sujet, la documentation est disponible [ici](https://guide.elm-lang.org/).
