## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Amusons-nous avec un peu d'assembleur !

---

### Introduction
Au cours de ce bref article, nous allons nous amuser à analyser du code assembleur. Pour ce faire, nous allons compiler un code C très basique, et étudier le code assembleur correspondant.
Le petit programme, très simple, demande un mot de passe et affiche un résultat en conséquence.

---

### Sommaire
* [Exemple de programme à désassembler](#exemple-de-programme-à-désassembler)
* [En avant avec GDB !](#en-avant-avec-gdb-)
* [Analyse du code](#analyse-du-code)
* [Conclusion](#conclusion)

---

### Exemple de programme à désassembler
Dans le cadre de ce petit exercice, voici le code que nous allons compiler :
```c
// pass.c

#include <string.h>  // memcpy, strcmp
#include <stdio.h>   // printf, scanf

#define INPUT_SIZE 50

int main() {
    // on demande un mot de passe à l'utilisateur
    char input[INPUT_SIZE];
    printf("Password?\n");
    scanf("%s", input);
    
    // on teste le mot de passe entré
    if (! strcmp(input, "PASSWORD")) {
        printf("Granted");
    } else {
        printf("Denied");
    }
}
```
Une fois compilé, le programme demande un mot de passe et affiche "Granted" si le mot de passe entré est valide, et "Denied" dans les autres cas.

Pour le compiler, nous utiliserons GCC : `gcc -g pass.c`.
Tentons à présent d'étudier le code assembleur correspondant. En bonus, amusons-nous à regarder comment, par hasard, lui faire afficher "Granted" même sans connaître le mot de passe attendu !

---

### En avant avec GDB !

Voyons avec GDB ce à quoi ressemble l'assembleur généré :
```asm
$ gdb -q a.out
Reading symbols from a.out...done.
(gdb) disass main
Dump of assembler code for function main:
   0x0000000000401550 <+0>:     push   rbp
   0x0000000000401551 <+1>:     mov    rbp,rsp
   0x0000000000401554 <+4>:     sub    rsp,0x60
   0x0000000000401558 <+8>:     call   0x401670 <__main>
   0x000000000040155d <+13>:    lea    rcx,[rip+0x2a9c]        # 0x404000
   0x0000000000401564 <+20>:    call   0x402ab0 <puts>
   0x0000000000401569 <+25>:    lea    rax,[rbp-0x40]
   0x000000000040156d <+29>:    mov    rdx,rax
   0x0000000000401570 <+32>:    lea    rcx,[rip+0x2a93]        # 0x40400a
   0x0000000000401577 <+39>:    call   0x402aa8 <scanf>
   0x000000000040157c <+44>:    lea    rax,[rbp-0x40]
   0x0000000000401580 <+48>:    lea    rdx,[rip+0x2a86]        # 0x40400d
   0x0000000000401587 <+55>:    mov    rcx,rax
   0x000000000040158a <+58>:    call   0x402a98 <strcmp>
   0x000000000040158f <+63>:    test   eax,eax
   0x0000000000401591 <+65>:    jne    0x4015a1 <main+81>
   0x0000000000401593 <+67>:    lea    rcx,[rip+0x2a7c]        # 0x404016
   0x000000000040159a <+74>:    call   0x402ab8 <printf>
   0x000000000040159f <+79>:    jmp    0x4015ad <main+93>
   0x00000000004015a1 <+81>:    lea    rcx,[rip+0x2a76]        # 0x40401e
   0x00000000004015a8 <+88>:    call   0x402ab8 <printf>
   0x00000000004015ad <+93>:    mov    eax,0x0
   0x00000000004015b2 <+98>:    add    rsp,0x60
   0x00000000004015b6 <+102>:   pop    rbp
   0x00000000004015b7 <+103>:   ret
End of assembler dump.
```
***Note :** Cet output correspond à ce qui a été compilé sur ma machine, qui tourne sous Windows. Pour voir l'équivalent sur Linux, c'est [par ici](https://gist.github.com/vpenando/70f7cd4189f1f49143c7a12f73f53894) !*

 Mieux encore, en passant l'option `/s` à `disass`, on peut afficher le code C correspondant !
```asm
(gdb) disass /s main
Dump of assembler code for function main:
pass.c:
8       int main() {
   0x0000000000401550 <+0>:     push   rbp
   0x0000000000401551 <+1>:     mov    rbp,rsp
   0x0000000000401554 <+4>:     sub    rsp,0x60
   0x0000000000401558 <+8>:     call   0x401670 <__main>

10          char input[INPUT_SIZE];
11          printf("Password?\n");
   0x000000000040155d <+13>:    lea    rcx,[rip+0x2a9c]        # 0x404000
   0x0000000000401564 <+20>:    call   0x402ab0 <puts>

12          scanf("%s", input);
   0x0000000000401569 <+25>:    lea    rax,[rbp-0x40]
   0x000000000040156d <+29>:    mov    rdx,rax
   0x0000000000401570 <+32>:    lea    rcx,[rip+0x2a93]        # 0x40400a
   0x0000000000401577 <+39>:    call   0x402aa8 <scanf>

15          if (! strcmp(input, "PASSWORD")) {
   0x000000000040157c <+44>:    lea    rax,[rbp-0x40]
   0x0000000000401580 <+48>:    lea    rdx,[rip+0x2a86]        # 0x40400d
   0x0000000000401587 <+55>:    mov    rcx,rax
   0x000000000040158a <+58>:    call   0x402a98 <strcmp>
   0x000000000040158f <+63>:    test   eax,eax
   0x0000000000401591 <+65>:    jne    0x4015a1 <main+81>

16              printf("Granted");
   0x0000000000401593 <+67>:    lea    rcx,[rip+0x2a7c]        # 0x404016
   0x000000000040159a <+74>:    call   0x402ab8 <printf>
   0x000000000040159f <+79>:    jmp    0x4015ad <main+93>

17          } else {
18              printf("Denied");
   0x00000000004015a1 <+81>:    lea    rcx,[rip+0x2a76]        # 0x40401e
   0x00000000004015a8 <+88>:    call   0x402ab8 <printf>
   0x00000000004015ad <+93>:    mov    eax,0x0

19          }
20      }
   0x00000000004015b2 <+98>:    add    rsp,0x60
   0x00000000004015b6 <+102>:   pop    rbp
   0x00000000004015b7 <+103>:   ret
End of assembler dump.
```
**Note :** À quoi correspond l'adresse `0x404000` et les adresses voisines ? Regardons ça : `objdump -M intel -D --no-show-raw-insn a.out | grep 404000`.
Parmi les résultats, nous voyons ceci :
```
0000000000404000 <.rdata>:
```
Or, `.rdata` équivaut grossièrement à la version en lecture seule du segment `.data`. Il s'agit donc des données constantes du programme. Nous pouvons par conséquent nous attendre à y trouver nos textes "Granted" et "Denied" ! Il y a donc fort à parier que le texte "Granted" se trouve à l'adresse `0x404016` et que "Denied" est quant à lui à l'adresse `0x40401e`. En effet, on charge ces adresses dans `rcx` avant les appels à `printf`, et la différence entre leurs adresses est de 8, soit la longueur de `Granted\0`, `\0` étant le caractère de fin de chaîne. A priori, il est possible d'établir la table de correspondance suivante :

|  Adresse   |   Chaîne    | 
|------------|-------------|
| `0x404000` | `Password?` |
| `0x40400a` | `%s`        |
| `0x40400d` | `PASSWORD`  |
| `0x404016` | `Granted`   |
| `0x40401e` | `Denied`    |

Nous expliquerons plus bas pourquoi la chaîne `Password?\n` a été remplacée par `Password?`.

**Note² :** Sur un système 32 bits, les registres ne seront pas `rax`, `rbx`, ... mais `eax`, `ebx` et autres. Les registres commençant pas `r`, tels que `rax` sont des registres 64 bits, tandis que ceux commençant par `e` (`eax` ou autres) sont des registres 32 bits.

Les trois premières lignes sont ce que l'on appelle le *prologue* de la fonction. Elles mettent de côté l'adresse actuelle du haut de la pile et allouent un espace d'une taille donnée. En somme, elles servent à mettre en place le contexte d'exécution de la fonction. 
```asm
push   rbp      ; on met rbp (base pointer) sur la pile
mov    rbp,rsp  ; on stocke rsp (stack pointer) dans rbp
sub    rsp,0x60 ; on alloue 0x60 (96) octets sur la pile
```
Les trois dernières, quant à elles, sont l'*épilogue* de la fonction. Elles restaurent la pile à son état d'origine.
```asm
add    rsp,0x60 ; on restaure rsp après avoir alloué 0x60 octets sur la pile
; autre possibilité :
; mov  rsp,rbp
pop    rbp      ; on restaure rbp, qui contenait la valeur de rsp
ret             ; on retourne de main()
 ```

---

### Analyse du code

Le code assembleur peut donc être segmenté ainsi :
```asm
; prologue
   0x0000000000401550 <+0>:     push   rbp
   0x0000000000401551 <+1>:     mov    rbp,rsp
   0x0000000000401554 <+4>:     sub    rsp,0x60

; fonction propre à GCC : nous ne nous attarderons pas dessus
   0x0000000000401558 <+8>:     call   0x401670 <__main>

; on demande un mot de passe à l'utilisateur
   0x000000000040155d <+13>:    lea    rcx,[rip+0x2a9c]        # 0x404000
   0x0000000000401564 <+20>:    call   0x402ab0 <puts>
   0x0000000000401569 <+25>:    lea    rax,[rbp-0x40]
   0x000000000040156d <+29>:    mov    rdx,rax
   0x0000000000401570 <+32>:    lea    rcx,[rip+0x2a93]        # 0x40400a
   0x0000000000401577 <+39>:    call   0x402aa8 <scanf>
   0x000000000040157c <+44>:    lea    rax,[rbp-0x40]
   0x0000000000401580 <+48>:    lea    rdx,[rip+0x2a86]        # 0x40400d
   0x0000000000401587 <+55>:    mov    rcx,rax

; c'est ici qu'on teste le mot de passe entré !
   0x000000000040158a <+58>:    call   0x402a98 <strcmp>
   0x000000000040158f <+63>:    test   eax,eax
   0x0000000000401591 <+65>:    jne    0x4015a1 <main+81>      ; ce saut correspond au 'else'
   0x0000000000401593 <+67>:    lea    rcx,[rip+0x2a7c]        # 0x404016
   0x000000000040159a <+74>:    call   0x402ab8 <printf>       ; ici, nous sommes dans le 'if' : on affiche "Granted" !
   0x000000000040159f <+79>:    jmp    0x4015ad <main+93>      ; on sort du bloc 'if/else' 
   0x00000000004015a1 <+81>:    lea    rcx,[rip+0x2a76]        # 0x40401e
   0x00000000004015a8 <+88>:    call   0x402ab8 <printf>       ; on affiche "Denied"
   0x00000000004015ad <+93>:    mov    eax,0x0                 ; on assigne 0 à eax (avec l'appel à 'ret' ci-après, équivaut à 'return 0')

; épilogue
   0x00000000004015b2 <+98>:    add    rsp,0x60
   0x00000000004015b6 <+102>:   pop    rbp
   0x00000000004015b7 <+103>:   ret                            ; on assigne 0 à eax (avec l'appel à 'ret' ci-après, équivaut à 'return 0')
 ```
 Il est intéressant de constater que le premier appel à `printf` est remplacé par un appel à `puts`. Ce dernier rajoutant un saut de ligne (caractère `\n`), le compilateur a optimisé l'appel à `printf("Password?\n")` en le remplaçant par `puts("Password?")`.
 
 Note sur `test eax, eax` :
* Si `eax & eax == 0`, alors le "zero flag" est setté à 1 ;
* L'instruction `je` exécute le *jump* si le "zero flag" vaut 1. `jne` fait le test inverse, à savoir si le "zero flag" vaut 0.

Cela équivaut donc à :
```c
if (eax != 0) {
    goto <main+81>;
}
```
Ainsi, pour accéder à la portion de code qui affiche "Granted", il suffit de remplacer `jne`. Ce faisant, aucun saut ne sera effectué, et toute entrée ne correspondant pas au mot de passe attendu exécutera l'affichage de "Granted" ! Nous entrerons donc dans le corps du `if` quoi qu'il arrive, et le code du `else` sera toujours ignoré.

---

### Conclusion

Au cours de cet article, nous avons analysé en surface un code assembleur basique. Nous avons même déterminé où se trouve la lecture du mot de passe demandé ! Ainsi, nous sommes théoriquement capables d'ignorer sa vérification ! À l'aide d'outils tels que objdump ou hexdump, il est certainement possible d'aller remplacer l'appel à `jne` par une suite de `nop` !
