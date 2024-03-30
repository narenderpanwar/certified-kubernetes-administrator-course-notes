# Network Policies

- Take me to [Video Tutorials](https://kodekloud.com/topic/network-policies-3/)
- Trafic flowing through a webserver serving frontend to users an app server serving backend API and a database server

![traffic](../../images/traffic.PNG)

- There are two types of traffic
  
  - Ingress
  - Egress
  
  ![ing1](../../images/ing1.PNG)

* By default, pods in a Kubernetes cluster can communicate with each other using IPs, pod names, or services.

* Kubernetes implements an "all-allow" rule by default, allowing traffic between any pods or services within the cluster.
  
  ![ing2](../../images/ing2.PNG)

## Network Security

![nsec](../../images/nsec.PNG)

## Network Policy

* Network policies are used to restrict communication between pods.
* Policies are defined as Kubernetes objects and linked to pods using labels and selectors.
* Policies specify rules for ingress (incoming) and egress (outgoing) traffic.

![npol](../../images/npol.PNG)

![npol1](../../images/npol1.PNG)

![npolsec](../../images/npolsec.PNG)

## Network Policy Rules

![npol2](../../images/npol2.PNG)

## Create network policy

- To create a network policy
  
  ```
  apiVersion: networking.k8s.io/v1
  kind: NetworkPolicy
  metadata:
   name: db-policy
  spec:
    podSelector:
      matchLabels:
        role: db
    policyTypes:
    - Ingress
    ingress:
    - from:
      - podSelector:
          matchLabels:
            role: api-pod
      ports:
      - protocol: TCP
        port: 3306
  ```
  
  ```
  $ kubectl create -f policy-definition.yaml
  ```

  ![npol3](../../images/npol3.PNG)

  ![npol4](../../images/npol4.PNG)

## Note

* Network policy enforcement depends on the network solution used in the Kubernetes cluster.
* Not all network solutions support network policies (e.g., Flannel).

  ![note1](../../images/note1.PNG)

#### Additional lecture on [Developing Networking Policies](https://kodekloud.com/topic/developing-network-policies/)

#### K8s Reference Docs

- https://kubernetes.io/docs/concepts/services-networking/network-policies/
- https://kubernetes.io/docs/tasks/administer-cluster/declare-network-policy/

