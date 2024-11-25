# Lab #1, 22110082, Nguyen Duc Tri, INSE330380E_01FIE
# Task 1: Transfer files between computers  
**Question 1**: 
Conduct transfering a single plaintext file between 2 computers, 
Using openssl to implementing measures manually to ensure file integerity and authenticity at sending side, 
then veryfing at receiving side. 


**Answer 1**:

## 1. Set up 2 Virtual Machines using docker: <br>
First, we need to have an image: <br>
```sh
docker pull ubuntu
```
Next, create docker containers: <br>
The first container is: 
```sh
docker run -it --name vm1 --hostname vm1 ubuntu
```

The second container is: <br>
```sh
docker run -it --name vm2 --hostname vm2 ubuntu
```

Check ip address of 2 sides: <br> 

```sh
docker inspect vm1
docker inspect vm2
```
![image](https://github.com/user-attachments/assets/2e5ce1bd-7162-44be-97f3-0d06c330e66b)

The ip address of vm1: `172.17.0.2` 

![image](https://github.com/user-attachments/assets/53c943ee-e28c-424a-b07b-f9dfddddd7a0)

The ip address of vm2: `172.17.0.3` 


## 2. Install Dependencies: <br>

First, we need to set up ssh in 2 sides: <br>
```sh
apt update
```

```sh
apt install openssh-server
```

```sh
apt install netcat-traditional
```

Next, enable ssh server and check it status using systemctl <br>
```sh
systemctl start ssh
systemctl status ssh
```

 
## 3. Create a text file named `plain.txt` in vm1 which is a `sender` in this case: <br>
```sh
echo "my name is sh1nata" > plain.txt
```
Check whether the `plain.txt` is available: 
```sh
cat plain.txt
```
![image](https://github.com/user-attachments/assets/39fc675c-1306-49e9-a790-5ea502d1d2e2)

## 4. Prerequisite a .hmac file using HMAC in sender `vm1`: <br>
To ensure the authenticity of a file using a Message Authentication Code (MAC), you can use a Hash-based Message Authentication Code (HMAC) along with a secret key. HMAC combines a hash function (such as SHA-256) with a secret key to create an authentication code, which helps ensure that the file has not been tampered with and that only someone with the secret key can generate a valid authentication code. <br> 

Create a HMAC file named `sender.hmac`: 
```sh
openssl dgst -sha256 -mac HMAC -macopt key:1410 -out sender.hmac plain.txt
```

Notice that the secret key I define here is: `1410`, which is the private key that only sender and receiver machines know about it. 
![image](https://github.com/user-attachments/assets/08571f18-7835-4bb8-80f0-036e6ab30ccf)

## 5. Transfering a `plain.txt` file between 2 machines: <br>

In receiver side, use the `nc` command, which is a `netcat-tradition` installed above to start receiving file: 

```sh
nc -l -p <port> > received_file.txt
```

In sender side, sending file using this command: 

```sh
cat plain.txt | nc <receiver_ip> <port>
```

## 6. Verrifying  file integerity and authenticity at receiving machine:

In receiver side, create HMAC of plain.txt in order to make a comparison with the `sender.hmac` that sender sends.
```sh
openssl dgst -sha256 -mac HMAC -macopt key:1410 -out receive.hmac plain.txt
```
Then compare them by: 
```sh 
cmp receive.hmac sender.hmac
```
Conclusion: Done

# Task 2: Transfering encrypted file and decrypt it with hybrid encryption. 
**Question 1**:
Conduct transfering a file (deliberately choosen by you) between 2 computers. 
The file is symmetrically encrypted/decrypted by exchanging secret key which is encrypted using RSA. 
All steps are made manually with openssl at the terminal of each computer.

**Answer 1**:
## 1. Creating a file in vm1 machine: <br>
```sh
echo "iam not good at exercise 1" > secret_file.txt
cat secret_file.txt
```

## 2. 


# Task 3: Firewall configuration
**Question 1**:
From VMs of previous tasks, install iptables and configure one of the 2 VMs as a web and ssh server. Demonstrate your ability to block/unblock http, icmp, ssh requests on the other host.
