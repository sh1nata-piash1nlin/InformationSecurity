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


## II. Stack frame visualization 

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

![image](https://github.com/user-attachments/assets/d2251655-af20-4b21-ac51-fe3b3de5d4ab)

6. 
   


