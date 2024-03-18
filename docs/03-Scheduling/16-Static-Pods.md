# Static Pods

- Static pods in Kubernetes are the pods that are directly managed by the kubelet daemon on a specific node, rather than being managed by the Kubernetes control plane.
- These pods are typically defined via static pod manifests located on the node's file system (default path - /etc/kubernetes/manifests) monitored by the kubelet.

#### How do you provide a pod definition file to the kubelet without a kube-apiserver?

- You can configure the kubelet to read the pod definition files from a directory on the server designated to store information about pods.

## Configure Static Pod

- The designated directory can be any directory on the host and the location of that directory is passed in to the kubelet as an option while running the service.
  
 - The option is named as **`--pod-manifest-path`**.
  
  ![sp](../../images/sp.PNG)

## Another way to configure static pod

- Instead of specifying the option directly in the **`kubelet.service`** file, you could provide a path to another config file using the config option, and define the directory path as staticPodPath in the file.
  
  ![sp1](../../images/sp1.PNG)

## View the static pods

- To view the static pods
  
  ```
  $ docker ps
  ```
  
  ![sp2](../../images/sp2.PNG)

#### The kubelet can create both kinds of pods - the static pods and the ones from the api server at the same time.

![sp3](../../images/sp3.PNG)

## Static Pods - Use Case

![sp4](../../images/sp4.PNG)

![sp5](../../images/sp5.PNG)

## Static Pods vs DaemonSets

![spvsds](../../images/spvsds.PNG)

#### K8s Reference Docs

- https://kubernetes.io/docs/tasks/configure-pod-container/static-pod/

