# Drawbacks of Storing Data Within Containers or Nodes in Kubernetes

1. **Persistence and Data Management**:
   
   - Containers are ephemeral by nature, meaning they can be spun up, destroyed, and replaced easily. Storing data within a container directly means that data is tied to the container's lifecycle. When the container is terminated, the data is lost unless explicitly saved elsewhere.
   - Similarly, storing data directly on the nodes where containers are running can lead to data loss if the node fails or the container gets rescheduled to a different node. This limits data persistence and introduces complexities in managing data consistency and availability.
2. **Scalability and Portability**:
   
   - Storing data within containers or directly on nodes can hinder scalability and portability. Containers should ideally be stateless and designed to be easily scalable and movable across different nodes in the cluster. Data tied to specific containers or nodes can make it harder to scale applications horizontally or migrate them between nodes.
3. **Resource Utilization and Isolation**:
   
   - Storing data within containers can increase the size of container images and consume more resources, affecting the performance and efficiency of the containerized application.
   - Storing data directly on nodes may compromise the isolation between different applications running on the same node, potentially leading to security and resource contention issues.
4. **Backup, Recovery, and Disaster Management**:
   
   - Data stored within containers or directly on nodes may lack proper backup, recovery, and disaster management mechanisms. In contrast, using external storage solutions like Persistent Volumes in Kubernetes allows for easier backup, snapshotting, and recovery procedures.
5. **Security and Compliance**:
   
   - Storing sensitive data within containers or on nodes without proper security measures can pose security risks and compliance issues. External storage solutions often provide features like encryption, access controls, and auditing to ensure data security and compliance with regulatory requirements.



# Persistent Volumes

- Take me to [Lecture](https://kodekloud.com/topic/persistent-volumes-4/)

In this section, we will take a look at **Persistent Volumes**

- In the large evnironment, with a lot of users deploying a lot of pods, the users would have to configure storage every time for each Pod.
- Whatever storage solution is used, the users who deploys the pods would have to configure that on all pod definition files in his environment. Every time a change is to be made, the user would have to make them on all of his pods.

![class-16](../../images/class16.PNG)

- A Persistent Volume is a cluster-wide pool of storage volumes configured by an administrator to be used by users deploying application on the cluster. The users can now select storage from this pool using Persistent Volume Claims.
  
  ```
  pv-definition.yaml
  
  kind: PersistentVolume
  apiVersion: v1
  metadata:
    name: pv-vol1
  spec:
    accessModes: [ "ReadWriteOnce" ]
    capacity:
     storage: 1Gi
    hostPath:
     path: /tmp/data
  ```
  
  ```
  $ kubectl create -f pv-definition.yaml
  persistentvolume/pv-vol1 created
  
  $ kubectl get pv
  NAME      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
  pv-vol1   1Gi        RWO            Retain           Available                                   3min
  
  $ kubectl delete pv pv-vol1
  persistentvolume "pv-vol1" deleted
  ```

#### Kubernetes Persistent Volumes

- https://kubernetes.io/docs/concepts/storage/persistent-volumes/
- https://portworx.com/tutorial-kubernetes-persistent-volumes/

