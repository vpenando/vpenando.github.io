#### [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Pile et tas, comment ça marche ?

---

## Introduction

---

## La pile

#### La pile, c'est quoi ?
La pile est un segment de la mémoire de type LIFO (**L**ast **I**n, **F**irst **O**ut).
Elle fonctionne via les registres RSP (**R**egister: **S**tack **P**ointer) et RBP (**R**egister: **B**ase **P**ointer), et les instruction `PUSH` et `POP`.

Au niveau de la mémoire, la pile est structurée comme suit :
```asm
HAUT DE LA PILE (adresses basses)
+--------------+
|  0x00001234  |  <- RSP (stack pointer)
|              |
|     ....     |
|              |
|  0x0000ffef  |
|  0x0000ffe7  |
|  0x0000ffff  | <- RBP (base pointer)
+--------------+
BAS DE LA PILE (adresses hautes)
```

Une allocation sur la pile consiste juste à effectuer une soustraction sur le "stack pointer".

Par exemple :
```asm
PUSH 42
PUSH 12
```
Cela revient à effectuer une soustraction de deux fois 8 octets sur la pile :
```asm
SUB rsp, 16
```
Ce qui équivaut à modifier la pile de la sorte :
```asm
|   Adresses   |   Valeurs    |
+--------------+--------------+
|  0x00001224  |      12      | <- RSP (stack pointer)
|  0x0000122c  |      42      |
|  0x00001234  |              | <- Ancienne valeur de RSP
|              |              |
|     ....     |     ....     |
|              |              |
|  0x0000ffff  |              | <- RBP (base pointer)
+--------------+--------------+
```


#### Pile & "stack frame"

Chaque fonction a son propre segment de la pile :
```c
void foo() {
    char array[0xff];
    // Plein de trucs avec 'array'...
    // ...
}
```
En entrant dans la fonction `foo`, un segment de la pile est alloué, correspondant à l'espace requis pour stocker ses variables locales (arguments compris).
Si l'on repgrend l'exemple ci-dessus, la pile aurait donc un état proche de :
```asm
|   Adresses   |   Valeurs    |
+--------------+--------------+
|  0x00001125  |              | <- RSP (stack pointer)
|              |              |
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
Ce qui, niveau machine correspond à l'opération suivante :
```asm
PUSH rbp       ; On sauvegarde le bas de pile
MOV  rbp, rsp  ; On démarre un nouveau segment à partir du haut de la pile
SUB  rsp, 0xff ; On alloue 255 octets
```
Le segment ainsi alloué correspond au "stack frame" de la fonction `foo`.
Lorsque l'on sort de cette fonction, l'ancien "stack frame" est restauré. 
