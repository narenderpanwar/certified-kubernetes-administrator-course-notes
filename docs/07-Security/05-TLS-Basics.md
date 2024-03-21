# TLS Basics

- Take me to [Video Tutorial](https://kodekloud.com/topic/tls-basics/)

In this section, we will take a look at TLS Basics

## Certificate

- A certificate is used to guarantee trust between 2 parties during a transaction.
- Example: when a user tries to access web server, TLS certificates ensure that the communication between them is encrypted and the server is who it says it is.
  
  ![cert1](../../images/cert1.PNG)

## Let's take a look at a scenario

- Without secure connectivity, if a user were to access his online banking application, the credentials he types in would be sent in a plain text format. The hacker sniffing network traffic could easily retrieve the credentials and use it to hack into the user's bank account. Well, that's obviously not safe.
  
  ![certificate](../../images/certificate.PNG)
- So you must encrypt the data being transferred using encryption keys.

## Symmetric Encryption

- The data is encrypted using a key, which is basically a set of random numbers and alphabets.
  ![certificate](../../images/se1.png)
- The data is then sent to the server. The hacker sniffing the network gets the data, but can't do anything with it.
- However, the same is the case with the server receiving the data. It cannot decrypt the data without the key.
- So a copy of the key must also be sent to the server so that the server can decrypt and read the message. But since the key is also sent over the same network, the attacker can sniff that as well and decrypt the data with it.
  
  ![certificate](../../images/se2.png)
- This is known as **`Symmetric Encryption`.** It is a secure way of encryption, but it uses the same key to encrypt and decrypt the data and the key has to be exchanged between the sender and the receiver, there is a risk of a hacker gaining access to the key and decrypting the data.

## Asymmetric Encryption

- Instead of using single key to encrypt and decrypt data, asymmetric encryption uses a pair of keys, a **`private key`** and a **`public key`** (say **`public lock`** for the sake of simplicity)
- A key, which is only with me, so it's private and a lock that anyone can access, so it's public.

![ae](../../images/ae1.png)

- If you encrypt or lock the data with your lock, you can only open it with the associated private key. So your private key must always be secure with you and not be shared with anyone else, it's private, but the lock is public and maybe shared with others, but they can only lock something with it.
  
  ![ae](../../images/ae2.png)
  
  ---
  
  Before we go back to our web server example, let's look at an even simpler use case of securing SSH access to servers using key pairs.
  
  - You have a server in your environment that you need access to and you don't want to use passwords as they're too risky so you decide to use key pairs.
  - You generate a public and private key pair by running the ssh-keygen command. It creates two files, ID_RSA is the private key and ID_RSA.pub is the public key. Well, not a public key, a public lock.
  - You then secure your server by locking down all access to it using public lock. It's usually done by adding an entry of your public key into the server's SSH authorized_keys file.
    
    ![ssh](../../images/ssh1.png)
  - If you want to SSH, you specify the location of your private key in your SSH command.
  - What if you have other servers in your environment? How do you secure more than one server with your key pair?
  - Well, you can create copies of your public lock and place them on as many servers as you want. You can use the same private key to SSH into all of your servers securely.
    ![cert3](../../images/cert3.PNG)
  - But what if other users need access to your servers?
  - Well, they can do the same thing. They can generate their own public and private key pairs. As the only person who has access to those servers, you can create an additional door for them and lock it with their public locks, copy their public locks to all the servers, and now other users can access the servers using their private keys.
    ![cert4](../../images/cert4.PNG)

---

- Let's go back to our web server example.
- The problem we had earlier with symmetric encryption was that the key used to encrypt data has to be sent to the server over the network along with the encrypted data, and so there is a risk of the hacker getting the key to decrypt the data.
  **`What if we could somehow get the key to the server safely?`**

![cert5](../../images/cert5.PNG)

![cert6](../../images/cert6.PNG)

#### How do you look at a certificate and verify if it is legit?

- who signed and issued the certificate.
- If you generate the certificate then you will have it sign it by yourself; that is known as self-signed certificate.
  
  ![cert7](../../images/cert7.PNG)

#### How do you generate legitimate certificate? How do you get your certificates singed by someone with authority?

- That's where **`Certificate Authority (CA)`** comes in for you. Some of the popular ones are Symantec, DigiCert, Comodo, GlobalSign etc.
  
  ![cert8](../../images/cert8.PNG)
  
  ![cert9](../../images/cert9.PNG)
  
  ![cert10](../../images/cert10.PNG)

## Public Key Infrastructure

![pki](../../images/pki.PNG)

## Certificates naming convention

![cert11](../../images/cert11.PNG)

