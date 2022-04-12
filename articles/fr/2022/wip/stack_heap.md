#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Pile et tas, comment ça marche ?

---

## Introduction

---

## La pile
#### a. La pile, c'est quoi ?
La pile est un segment de la mémoire de type LIFO (**L**ast **I**n, **F**irst **O**ut).
Elle fonctionne via les registres RSP (**R**egister: **S**tack **P**ointer) et RBP (**R**egister: **B**ase **P**ointer), et les instruction `PUSH` et `POP`.

RSP, le "stack pointer", pointe sur le haut de la pile. Quant à RBP, le "base pointer", il correspond au bas de la pile.

Au niveau de la mémoire, la pile est structurée comme suit :
```asm
HAUT DE LA PILE (adresses basses)
+--------------+
|  0x00001234  |  <- RSP (stack pointer)
|              |
|     ....     |
|              |
|  0x0000ffef  |
|  0x0000fff7  |
|  0x0000ffff  | <- RBP (base pointer)
+--------------+
BAS DE LA PILE (adresses hautes)
```
Une allocation sur la pile consiste juste à effectuer une soustraction sur le "stack pointer".

Lorsque vous déclarez, par exemple, deux variables de type `int64_t` en C, comme ceci :
```c
int64_t a = 42;
int64_t b = 12;
```
Cela revient à effectuer une soustraction de deux fois 8 octets sur la pile pour y placer ces valeurs :
```asm
|   Adresses   |   Valeurs    |
+--------------+--------------+
|  0x00001224  |    42 (a)    | <- RSP (stack pointer)
|  0x0000122c  |    12 (b)    |
|  0x00001234  |              | <- Ancienne valeur de RSP
|              |              |
|     ....     |     ....     |
|              |              |
|  0x0000ffff  |              | <- RBP (base pointer)
+--------------+--------------+
```
***Note -** Les variables sont généralement empilées dans l'ordre inverse de leur déclaration, expliquant l'ordre du schéma ci-dessus.*

Dans la plupart des langages de programmation, les variables locales sont stockées sur la pile.
Une allocation a lieu sur la pile au début de chaque fonction, afin de créer le "stack frame" approprié.

#### b. Pile & "stack frame"
D'une manière générale, chaque fonction a son propre segment de la pile.
Prenons en exemple la fonction suivante :
```c
void foo() {
    char array[0xff];
    // Plein de trucs avec 'array'...
    // ...
}
```
En entrant dans la fonction `foo`, un segment de la pile est alloué, correspondant à l'espace requis pour stocker ses variables locales (arguments compris).
Si l'on reprend l'exemple de la partie précédente, la pile aurait donc un état proche de :
```asm
|   Adresses   |   Valeurs    |
+--------------+--------------+
|  0x00001125  |              | <- RSP (stack pointer)
|              |              |
|     ....     |     ....     |
|     ....     |  255 octets  |
|     ....     |     ....     |
|              |              |
|  0x00001224  |      12      | <- RBP, et ancienne valeur de RSP
|  0x0000122c  |      42      |
|  0x00001234  |              |
|              |              |
|     ....     |     ....     |
|              |              |
|  0x0000ffff  |              | <- RBP (base pointer)
+--------------+--------------+
```
Ce qui, niveau machine, correspond aux instructions suivantes :
```asm
PUSH rbp       ; On sauvegarde le bas de pile
MOV  rbp, rsp  ; On démarre un nouveau segment à partir du haut de la pile
SUB  rsp, 0xff ; On y alloue 255 octets en décalant le haut de la pile d'autant
```
Le segment ainsi alloué correspond au "stack frame" de la fonction `foo`.
Ces opérations sont effectuées au début de la plupart des fonctions, et constituent le **prologue** d'une fonction.

Lorsque l'on sort de cette fonction, l'ancien "stack frame" est restauré via les opérations suivantes :
```asm
ADD rsp, 0xff ; On décale le haut de la pile de 255 octets vers le bas
POP rbp       ; On restaure la valeur de RBP "PUSHée" dans le prologue
```
Ces opérations constituent l'**épilogue** d'une fonction, et visent à restaurer l'état de la pile tel qu'il était auparavant.