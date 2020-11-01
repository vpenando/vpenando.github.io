## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

### (FR) Introduction à `go test`

---

### Sommaire
* Introduction
* Go et tests
* Le package `testing`
* Présentation du package `assert`
* Conclusion

---

### Introduction

Coder, c'est bien. Toutefois, produire quelque chose qui fonctionne comme attendu, c'est encore mieux. Ainsi, il est de bon goût de tester notre code afin de s'assurer que
notre output fait bien ce que l'on attend de lui et que tous les cas tordus ont bien été prévus. À cet effet, plusieurs catégories de tests existent : tests unitaires, tests manuels, tests de non régression... Chacune a ses intérêts, qui sont tous complémentaires.

Je ne connais aucun programmeur qui *aime* tester, mais tous s'accordent à dire qu'il n'y a pas de meilleure manière pour assurer la stabilité d'un programme. Cela nous rajoute du travail à court terme, mais nous permet d'éviter l'incendie au lieu de devoir l'éteindre.

Ne pas tester son code, c'est comme ne tester personne lors d'une épidémie et dire que personne n'est malade.

<img src="this_is_fine_go.png" align="center" />

*([Source](https://github.com/ashleymcnamara/gophers))*

Go embarque dans sa bibliothèque standard un package qui fournit un environnement de test clés en main et très simple d'utilisation. Au cours de cet article, nous verrons comment l'utiliser, et comment enrichir nos tests grâce à un package tiers.

---

### Le package `testing`

https://golang.org/pkg/testing/

---

### Présentation du package `assert`

https://godoc.org/github.com/stretchr/testify/assert

---

### Conclusion
