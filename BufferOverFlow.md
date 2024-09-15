"""
@author: Nguyen Duc Tri 
@id: 22110082
"""

# HCMUTE Information Security Lab 
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

For exploiting it, just filling up the buffer 200 bytes, EBP, and placing the memory address of secretFunc() into EIP. 
Instead of returning to its original position to continue the normal program flow, the EIP will now point to the secretFunc() and execute it

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

   



