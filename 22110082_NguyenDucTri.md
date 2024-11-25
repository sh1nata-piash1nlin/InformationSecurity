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

## 4. Prerequisites for a .hmac file using HMAC in sender `vm1`: <br>
To ensure the authenticity of a file using a Message Authentication Code (MAC), you can use a Hash-based Message Authentication Code (HMAC) along with a secret key. HMAC combines a hash function (such as SHA-256) with a secret key to create an authentication code, which helps ensure that the file has not been tampered with and that only someone with the secret key can generate a valid authentication code. <br> 

Create a HMAC file named `sender.hmac`: 
```sh
openssl dgst -sha256 -mac HMAC -macopt key:1410 -out lol.hmac plain.txt
```

Notice that the secret key I define here is: `1410`, which is the private key that only sender and receiver machines know about it. 
![image](https://github.com/user-attachments/assets/08571f18-7835-4bb8-80f0-036e6ab30ccf)

## 5. Transfering a `plain.txt` file between 2 machines: <br>

In receiver side, use the `nc` command, which is a `netcat-tradition` installed above to start receiving file: 

```sh
nc -l -p <port> > received_file.txt
nc -l -p 3034 > lol.hmac
```
![image](https://github.com/user-attachments/assets/2919de21-699b-49ea-b9ad-4bb6aa683f5e)


![image](https://github.com/user-attachments/assets/dde46cc5-a9a2-4c12-aa18-2a67c34be01f)

In sender side, sending file using this command: 

```sh
cat plain.txt | nc <receiver_ip> <port>
cat lol.hmac | nc <receiver_ip> <port> 
```

Next, check if all the files have been catched:
![image](https://github.com/user-attachments/assets/49c630dc-8173-472c-a22a-795eec380cab)


## 6. Verrifying  file integerity and authenticity at receiving machine:

In receiver side, create HMAC of plain.txt in order to make a comparison with the `sender.hmac` that sender sends.
```sh
openssl dgst -sha256 -mac HMAC -macopt key:1410 -out receiver.hmac plain.txt
```

Then compare them by: 
```sh 
cmp receiver.hmac lol.hmac 
```
![image](https://github.com/user-attachments/assets/2b6dc9f1-5606-4816-ab1c-2fe40101e6cb)

We can see that there is no difference because nothing is printed out. 

Conclusion: Done

# Task 2: Transfering encrypted file and decrypt it with hybrid encryption. 
**Question 1**:
Conduct transfering a file (deliberately choosen by you) between 2 computers. 
The file is symmetrically encrypted/decrypted by exchanging secret key which is encrypted using RSA. 
All steps are made manually with openssl at the terminal of each computer.

**Answer 1**:
## 1. Creating a file in vm1 machine on sender: <br>
```sh
echo "iam not good at exercise 1" > secret_file.txt
cat secret_file.txt
```
![image](https://github.com/user-attachments/assets/f4a99a56-d431-4499-a94c-3bbd342cbfc4)

## 2. Generate RSA Key Pair on both machines: 
```sh
openssl genpkey -algorithm RSA -out private_key.pem -pkeyopt rsa_keygen_bits:2048
openssl rsa -in private_key.pem -pubout -out public_key.pem
```
Explaination: We need to generate RSA keys because they are the mechanism that allows us to securely exchange the symmetric AES key used for encrypting the file. <br>

`private_key.pem`: Keep this private! Used for decrypting the symmetric key.  <br>
`public_key.pem`: Share this with Computer 1 for encrypting the symmetric key.  <br>

![image](https://github.com/user-attachments/assets/744a1d8e-1e1f-4ca1-960e-16a11fe896c3)

![image](https://github.com/user-attachments/assets/3f078d2c-99bc-4742-910c-bacf8369d494)

Showing private_key: 
```sh
cat privatekey_pem
```
![image](https://github.com/user-attachments/assets/d32b598b-d74e-4057-9471-2b675f3d097f)

The same step is conducted on receiver machine. I name the private key as `receiver_private_key.pem` and public key as `receiver_public_key.pem`. 

## 3. Generate a Symmetric Key on sender vm1: <br> 

The Sender will encrypt a file (secret_file.txt) using AES-256 that saved in `sysmmetric_key.txt`.
```sh
openssl rand -hex 32 > symmetric_key.txt
```
This file (symmetric_key.txt) contains the symmetric key.

![image](https://github.com/user-attachments/assets/8af687fd-3729-487e-ba89-afd7ca0df08c)


## 4.  Encrypt the File with the Symmetric Key using AES on sender vm1: 

We need to encrypt the file using AES (Advanced Encryption Standard) first because AES is a symmetric encryption algorithm that is optimized for efficiency when dealing with large amounts of data, such as files.

```sh
openssl enc -aes-256-ecb -in secret_file.txt -out secret.enc -kfile symmetric_key.txt -pbkdf2
```
The reason I add `-pbkdf2` which is short for Password-Based Key Derivation Function 2 that used to derive the encryption key from the password provided in `symmetric_key.txt`.

![image](https://github.com/user-attachments/assets/884e0380-166d-4f7b-9f8a-5a93d1f6c733)


## 5. Encrypt the AES Key with Receiver's RSA public key: 
Next, the Sender will encrypt the AES key (symmetric_key.txt) using the Receiver's RSA public key, so it can be securely transferred.
```sh
openssl pkeyutl -encrypt -inkey receiver_public_key.pem -pubin -in symmetric_key.txt -out symmetric_key.enc
```
![image](https://github.com/user-attachments/assets/d1e51a3e-5db3-4568-9fee-d57497d71da1)


## 6. Transfer Files from VM1 to VM2:
I will send `symmetric_key.enc` and `secret.enc` using netcat:  

In receiver side: 
```sh
nc -l -p 1410 > enc.txt
nc -l -p 1410 > enc_file.txt

```

In sender side: 
```sh
cat symmetric_key.enc | nc 172.17.0.3 1410
cat secret.enc | nc 172.17.0.3 1410
```

## 7. Receiver Decrypts the AES Key Using Their RSA Private Key: 
```sh
openssl rsautl -decrypt -inkey receiver_private_key.pem -in symmetric_key.enc -out symmetric_key_decrypted.txt
```

## 8. Receiver Decrypts the File Using the Decrypted AES Key: 
Finally, the Receiver will use the decrypted AES key (symmetric_key_decrypted.txt) to decrypt the file.
```sh
openssl enc -d -aes-256-cbc -in secret_file.enc -out secret_file_decrypted.txt -k $(cat symmetric_key_decrypted.txt)

```


# Task 3: Firewall configuration
**Question 1**:
From VMs of previous tasks, install iptables and configure one of the 2 VMs as a web and ssh server. Demonstrate your ability to block/unblock http, icmp, ssh requests on the other host.

**Answer 1**:

I will choose VM1 as a web and ssh server on this exercise <br> 

## 1. Install IP tables for both machines: 

```sh
apt install iptables -y
```

## 2. Set Up Web and SSH Server on VM1: 

On VM1, install Apache to serve HTTP requests (web server):
```sh
apt install apache2 -y
```

Install the SSH server to accept SSH requests:
```sh
apt install openssh-server -y
```





