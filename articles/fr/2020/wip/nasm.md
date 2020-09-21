## [Welcome here!](https://vpenando.github.io) | [Articles](https://vpenando.github.io/articles.html) | [Main projects](https://vpenando.github.io/projects.html) | [About me](https://vpenando.github.io/about.html)

## (FR) Amusons-nous avec NASM !

---

### Sommaire


---

### Introduction
* Note sur x86 / x64 : rax/eax, rbx/ebx, ...

---

### Exemple de programme à désassembler
```c
// pass.c

#include <stdio.h>   // printf, scanf
#include <string.h>  // memcpy, strcmp

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

Compiler : `gcc -g pass.c`

Voyons avec GDB ce à quoi ressemble l'assembleur généré :
```asm
$ gdb -q ./a.out
(gdb) disass main
Dump of assembler code for function main:
   0x0000000000401550 <+0>:     push   rbp
   0x0000000000401551 <+1>:     mov    rbp,rsp
   0x0000000000401554 <+4>:     sub    rsp,0x60
   0x0000000000401558 <+8>:     call   0x401670 <__main>
   0x000000000040155d <+13>:    lea    rcx,[rip+0x2a9c]        # 0x404000
   0x0000000000401564 <+20>:    call   0x402ab0 <puts>
   0x0000000000401569 <+25>:    lea    rax,[rbp-0x40]
   0x000000000040156d <+29>:    mov    rcx,rax
   0x0000000000401570 <+32>:    call   0x402aa8 <scanf>
   0x0000000000401575 <+37>:    lea    rax,[rbp-0x40]
   0x0000000000401579 <+41>:    lea    rdx,[rip+0x2a8a]        # 0x40400a
   0x0000000000401580 <+48>:    mov    rcx,rax
   0x0000000000401583 <+51>:    call   0x402a98 <strcmp>
   0x0000000000401588 <+56>:    test   eax,eax
   0x000000000040158a <+58>:    jne    0x40159a <main+74>
   0x000000000040158c <+60>:    lea    rcx,[rip+0x2a80]        # 0x404013
   0x0000000000401593 <+67>:    call   0x402ab8 <printf>
   0x0000000000401598 <+72>:    jmp    0x4015a6 <main+86>
   0x000000000040159a <+74>:    lea    rcx,[rip+0x2a7a]        # 0x40401b
   0x00000000004015a1 <+81>:    call   0x402ab8 <printf>
   0x00000000004015a6 <+86>:    mov    eax,0x0
   0x00000000004015ab <+91>:    add    rsp,0x60
   0x00000000004015af <+95>:    pop    rbp
   0x00000000004015b0 <+96>:    ret
End of assembler dump.
```
 Mieux encore, en passant l'option `/m` à `disass`, on peut afficher le code C correspondant !
```asm
(gdb) disass /m main
Dump of assembler code for function main:
warning: Source file is more recent than executable.
6       int main() {
   0x0000000000401550 <+0>:     push   rbp
   0x0000000000401551 <+1>:     mov    rbp,rsp
   0x0000000000401554 <+4>:     sub    rsp,0x60
   0x0000000000401558 <+8>:     call   0x401670 <__main>

7           char input[INPUT_SIZE];
8           int allowed = 0;
   0x000000000040155d <+13>:    lea    rcx,[rip+0x2a9c]        # 0x404000
   0x0000000000401564 <+20>:    call   0x402ab0 <puts>

9           printf("Password?\n");
   0x0000000000401569 <+25>:    lea    rax,[rbp-0x40]
   0x000000000040156d <+29>:    mov    rcx,rax
   0x0000000000401570 <+32>:    call   0x402aa8 <scanf>

10          scanf("%s", input);
   0x0000000000401575 <+37>:    lea    rax,[rbp-0x40]
   0x0000000000401579 <+41>:    lea    rdx,[rip+0x2a8a]        # 0x40400a
   0x0000000000401580 <+48>:    mov    rcx,rax
   0x0000000000401583 <+51>:    call   0x402a98 <strcmp>
   0x0000000000401588 <+56>:    test   eax,eax
   0x000000000040158a <+58>:    jne    0x40159a <main+74>

11          if (! strcmp(input, "PASSWORD")) {
   0x000000000040158c <+60>:    lea    rcx,[rip+0x2a80]        # 0x404013
   0x0000000000401593 <+67>:    call   0x402ab8 <printf>
   0x0000000000401598 <+72>:    jmp    0x4015a6 <main+86>

12              printf("Granted");
13          } else {
   0x000000000040159a <+74>:    lea    rcx,[rip+0x2a7a]        # 0x40401b
   0x00000000004015a1 <+81>:    call   0x402ab8 <printf>
   0x00000000004015a6 <+86>:    mov    eax,0x0

14              printf("Denied");
15          }
   0x00000000004015ab <+91>:    add    rsp,0x60
   0x00000000004015af <+95>:    pop    rbp
   0x00000000004015b0 <+96>:    ret

End of assembler dump.
```
Le code assembleur peut donc être segmenté ainsi :
```asm
; prologue
push   rbp
mov    rbp,rsp
sub    rsp,0x60

call   0x401670 <__main>
lea    rcx,[rip+0x2a9c]        # 0x404000
call   0x402ab0 <puts>
lea    rax,[rbp-0x40]
mov    rcx,rax
call   0x402aa8 <scanf>
lea    rax,[rbp-0x40]
lea    rdx,[rip+0x2a8a]        # 0x40400a
mov    rcx,rax
call   0x402a98 <strcmp>
test   eax,eax
jne    0x40159a <main+74>
lea    rcx,[rip+0x2a80]        # 0x404013
call   0x402ab8 <printf>
jmp    0x4015a6 <main+86>
lea    rcx,[rip+0x2a7a]        # 0x40401b
call   0x402ab8 <printf>
mov    eax,0x0

; épilogue
add    rsp,0x60
pop    rbp
ret
 ```
Les trois premières lignes sont le *prologue* de la fonction. Elles servent à mettre en place le contexte d'exécution de la fonction.
```asm
push   rbp      ; on met rbp (base pointer) sur la pile
mov    rbp, rsp  ; on stocke rsp (stack pointer) dans rbp
sub    rsp, 0x60 ; on alloue 0x60 (96) octets sur la pile
```
Les trois dernières, quant à elles, sont l'*épilogue* de la fonction. Elles restaurent la pile à son état d'origine.
```asm
add    rsp, 0x60 ; on restaure rsp après avoir alloué 0x60 octets sur la pile
pop    rbp      ; on restaure rbp, qui contenait la valeur de rsp
ret             ; on retourne de main()
 ```
 En 32 bits, l'épilogue ressemblerait à ceci :
 ```asm
mov    esp, ebp ; on restitue l'ancienne valeur de esp
pop    ebp      ; on restaure ebp
ret             ; on retourne de main()
 ```



Note sur `test eax, eax` et `je` :
* Si `eax & eax == 0`, alors le "zero flag" est setté à 1 ;
* L'instruction `je` exécute le *jump* si le "zero flag" vaut 1. `jne` fait le test inverse, à savoir si le "zero flag" vaut 0.

Cela équivaut donc à :
```c
if (eax != 0) {
    goto <main+74>;
}
```
