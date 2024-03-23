# TLS in Kubernetes

- Take me to [Video Tutorial](https://kodekloud.com/topic/tls-in-kubernetes/)

In this section, we will take a look at TLS in kubernetes

- In the previous lecture we learned what public and private keys are. How a server uses public and private keys to secure connectivity. We will call them server certificates. We saw what a Certificate Authority is. We learned that the CA has its own set of public and private keepers that it uses to sign server certificates. We will call them root certificates. We also saw how a server can request a client to verify themselves using client certificates.
- So, there are three types of certificates:
  
  - **`Server certificates`** configured on the servers,
  - **`Root certificate`** configured on the CA servers,
  - **`Client certificates`** configured on the clients.
  
  ![tls](../../images/tls.png)
- We will now see how these concepts relate to a Kubernetes cluster. The Kubernetes cluster consists of a set of master and worker nodes. Of course, all communication between these nodes need to be secure and must be encrypted. All interactions between all services and their clients need to be secure.
  
  ![tls](../../images/tls1.png)
  ![tls](../../images/tls2.png)
  ![tls](../../images/tls3.png)
- So that's too many certificates. Let's try and group them.
  ![certs](../../images/certs.PNG)
  

