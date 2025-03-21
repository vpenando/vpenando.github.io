#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Pourquoi vous devriez utiliser Go

---

### Sommaire
* [Introduction](#introduction)
* [Une syntaxe simple](#syntax)
* [Un langage compilé](#compiled-lang)
* [Un langage statiquement typé](#typed-lang)
* [Un système de dépendances puissant](#dependencies)
* [Des tas d'applications](#applications)
* [Conclusion](#conclusion)

---

### <a name="introduction">Introduction</a>

Parmi les différents langages de programmation existant, certains se démarquent d'une manière ou d'une autre : élégance, performances, tooling... il y aura toujours un langage adapté à vos besoins et à vos goûts.

L'un de ces langages combine selon moi tous ces avantages, et comme vous l'avez lu dans le titre, je veux bien évidemment parler de Go.

Cet article ne se veut pas être un tutoriel, mais plutôt une liste non exhaustive et non ordonnée des intérêts qu'il présente, et de pourquoi ce langage peut vous intéresser vous aussi.

---

### <a name="syntax">Une syntaxe simple</a>

Go se veut être un langage sur lequel *n'importe qui* peut monter en compétence rapidement.
Sa syntaxe volontairement simpliste permet une transition et une montée en compétences très rapides, parfait pour un débutant.

Il est ainsi très facile de comprendre une codebase existante, et de vite devenir productif sur un projet !

Par ailleurs, [le faible nombre de mots-clés du langage](https://go.dev/ref/spec#Keywords) contribue à une prise en main très rapide, car ils existent pour la plupart déjà dans les langages les plus courants.
Quant à la visibilité des types, variables et fonctions, [elle est simplement déterminée par le premier caractère](https://go.dev/doc/effective_go#names), ce qui peut être quelque peu déroutant au premier abord, j'en conviens.

---

### <a name="compiled-lang">Un langage compilé</a>

En effet, Go a beau ressembler à un langage de script, il est compilé !
Pas d'interpréteur, de VM (JVM, .NET...) ou autre diablerie, le code est compilé et, cerise sur le gâteau, **en natif** !
En résultent de hautes performances, presque comparables à du C++, mais sans le bruit et la complexité.

Et surtout, contrairement à C, C++ ou encore Rust, la compilation d'un code Go est **presque instantanée**.
Cela est notamment dû à sa syntaxe, que nous avons évoquée plus haut.
Sa compilation est tellement rapide que certains l'utilisent comme langage de script, via la commande [`go run`](https://pkg.go.dev/cmd/go#hdr-Compile_and_run_Go_program).

Par ailleurs *-sauf cas particuliers-* tout programme écrit en Go embarque toutes les dépendances dont il a besoin ; ce qui signifie que le binaire compilé n'a besoin d'aucune DLL pour fonctionner !

Enfin, il est possible de **compiler pour une architecture différente de celle de la machine courante** : par exemple, vous pouvez aisément depuis Windows créer un exécutable à destination d'une machine tournant sur Linux !

Et en plus, le tout tient en une seule ligne de commande :
```cmd
env GOOS=linux GOARCH=arm64 go build
```

Enfin, qui dit compilation dit vérifications (notamment des types) au *compile time*, et donc un traitement en moins au moment de l'exécution.

---

### <a name="typed-lang">Un langage statiquement typé</a>

Go est *-si l'on oublie `any`-* statiquement et fortement typé : cela signifie qu'une variable n'a qu'un et un seul type, et que des conversions implicites ne sont pas permises.

Par exemple, le code suivant :
```go
var x int = 42
var y float64 = 12.3
y = x
```
provoquera l'erreur de compilation suivante :
```
cannot use x (variable of type int) as float64 value in assignment
```
Ainsi, pas de risque de conversions implicites involontaires !

Et il est possible de profiter de cette force pour créer des **alias fortement typés**, comme par exemple des "strongly-typed IDs" :
```go
import "github.com/google/uuid"

type UserID uuid.UUID

type BankAccountID uuid.UUID
```
Ici, les deux types sont des alias de `uuid.UUID`, mais ne sont pas comparables entre eux car ce sont bel et bien des types *différents*.

---

### <a name="dependencies">Un système de dépendances puissant</a>

Dans tout programme d'un minimum d'envergure, il est nécessaire d'avoir des dépendances tierces.
Or, dans certains langages (coucou aux amateurs de C++), installer des dépendances est juste horrible quand on n'a pas l'habitude.
Qu'il s'agisse d'[UUID](https://github.com/google/uuid), [un système de tests expressifs](https://github.com/stretchr/testify/assert) ou autre, installer des dépendances en Go est on ne peut plus simple via la commande [`go get`](https://pkg.go.dev/cmd/go#hdr-Add_dependencies_to_current_module_and_install_them) :
```cmd
go get github.com/google/uuid
```
Par ailleurs, toute dépendance installée en Go est sous forme de code source, et non d'un fichier compilé opaque ; or, c'est un avantage non négligeable de pouvoir examiner le code source d'un package quand celui-ci n'est pas suffisamment documenté, et je parle en connaissance de cause !
Et, comme dit plus haut, la syntaxe très sobre de Go fait qu'il n'y a aucune difficulté à se plonger dans une codebase tierce.

---

### <a name="applications">Des tas d'applications</a>

- CLI (+ `go run`)
- Web API
- Embedded

---

### <a name="conclusion">Conclusion</a>
