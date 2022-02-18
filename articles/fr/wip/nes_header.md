

#### [FR] Création d'un émulateur NES #1 - Comment est structuré un header de jeu NES ?

###### Note
> Cet article est le premier d'une série visant à créer pas à pas un émulateur NES en C++.

#### Que contient une ROM NES ?
```
1. Header (16 bytes)
2. Trainer, if present (0 or 512 bytes)
3. PRG ROM data (16384 * x bytes)
4. CHR ROM data, if present (8192 * y bytes)
```

#### Que contient le header d'une ROM NES ?
```
0-3: Constant $4E $45 $53 $1A ("NES" followed by MS-DOS end-of-file)
4: Size of PRG ROM in 16 KB units
5: Size of CHR ROM in 8 KB units (Value 0 means the board uses CHR RAM)
6: Flags 6
7: Flags 7
8: Size of PRG RAM in 8 KB units (Value 0 infers 8 KB for compatibility; see PRG RAM circuit)
9: Flags 9
10: Flags 10 (unofficial)
11-15: Zero filled
```

#### Lecture d'une ROM
Tout d'abord, voici un petit script qui fait une chose très simple : il se contente d'afficher (en hexadécimal) le header d'une ROM NES.
```py
#!/usr/bin/env python
# rom_header.py
import sys

ROM_HEADER_SIZE = 16

with open(sys.argv[1], "rb") as f:
    header = list(f.read(ROM_HEADER_SIZE))
    for byte in header:
        print(format(byte, '02x'))
```
***Note** - Ce script est volontairement allégé (pas de vérification sur la validité du chemin, taille de la ROM, etc...) pour plus de lisibilité.*

Pour l'exécuter, c'est très simple :
```sh
chmod +x rom_header.py
./rom_header.py <ROM>
```
Exemple d'output :
```sh
> ./rom_header.py "***.nes"
4e
45
53
1a
02
01
01
00
00
00
00
00
00
00
00
00
```
