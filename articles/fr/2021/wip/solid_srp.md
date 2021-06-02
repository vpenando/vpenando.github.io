## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) SOLID 1/5 - Le SRP

---

### Introduction

Plusieurs mois se sont écoulés depuis que j'ai annoncé travailler sur une synthèse des principes SOLID.
En effet, en plus d'être toujours ponctuel (hein, comment ça je mens mal ?), j'essaie avant tout de tenir parole.

Mais récapitulons : SOLID, ça veut dire quoi ? Il s'agit d'un acronyme désignant... d'autres acronymes.
Bon, pour être plus précis, il s'agit de cinq grands principes à respecter en programmation, si l'on veut avoir un code qui soit un minimum lisible, maintenable et évolutif.
Ils ne garantissent à eux seuls absolument pas une architecture exemplaire, mais offrent de bonnes bases pour concevoir quelque chose de viable.

Ces fameux principes sont les suivants :
* SRP (**S**ingle **R**esponsibility **P**rinciple) ;
* OCP (**O**pen/**C**losed **P**rinciple) ;
* LSP (**L**iskov **S**ubstitution **P**rinciple) ;
* ISP (**I**nterface **S**egregation **P**rinciple) ;
* DIP (**D**ependency **I**nversion **P**rinciple).

Soit, dans la langue de Kev Adams :
* Principe de responsabilité unique ;
* Principe ouvert/fermé ;
* Principe de substitution de Liskov ;
* Principe de ségrégation des interfaces ;
* Principe d'investion de dépendances.

Au cours de cet article, nous présenterons le premier de ces principes, à savoir le principe de responsabilité unique.

---

### La théorie

En théorie, le SRP pourrait être énoncé ainsi :
> Toute entité *-qu'il s'agisse d'une classe, d'une fonction, ...-* doit avoir **une et une seule** responsabilité. Elle doit rendre **un** service, et **bien** le rendre.

