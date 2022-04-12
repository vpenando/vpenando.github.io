


Etat de la pile au niveau des adresses mémoires :
```
HAUT DE LA PILE (adresses basses)
+--------------+
|  0x00001234  |  <- RSP (stack pointer)
|              |
|     ....     |
|              |
|  0x0000ffef  |
|  0x0000ffe7  |
|  0x0000ffff  |
+--------------+
BAS DE LA PILE (adresses hautes)
```

Une allocation sur la pile consiste juste à effectuer une soustraction sur le "stack pointer".

Par exemple :
```asm
PUSH 42
PUSH 12
```
Equivaut à modifier la pile de la sorte :
```
|   Adresses   |   Valeurs    |
+--------------+--------------+
|  0x00001224  |      12      | <- RSP (stack pointer)
|  0x0000122c  |      42      |
|  0x00001234  |              | <- Ancien RSP
|              |              |
|     ....     |     ....     |
|              |              |
+--------------+--------------+
```
