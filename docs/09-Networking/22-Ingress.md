# Ingress

- We will start with a simple scenario.
- You are deploying an application on Kubernetes for a company that has an online store selling products. Your application would be available at say, myonlinestore.com. You build the application into a docker image and deploy it on the Kubernetes cluster as a pod in a deployment. Your application needs a database so you deploy a My SQL database as a pod and create a service of type cluster IP called my SQL Service to make it accessible to your application. Your application is now working. To make the application accessible to the outside world, you create another service, this time of type node port, and make your application available on a high port on the nodes in the cluster.
  ![ingress](../../images/ingress.png)
- In this example, a port 38080, is allocated for the service. The users can now access your application using the URL HTTP://< IP of any of your nodes >, followed by the port 38080.
- That setup works and users are able to access the application. Whenever traffic increases, we increase the number of replicas of the pod to handle the additional traffic, and the service takes care of splitting traffic between the pods.
- __However, if you have deployed a production grade application before you know that there are many more things involved in addition to simply splitting the traffic between the pods.__
- For example, we do not want the users to have to type in IP address every time. So you configure your DNS server to point to the IP of the nodes.
  
  ![ingress](../../images/ingress1.png)
- Your users can now access your application using the URL myonlinestore.com and port 38080.



In this section, we will take a look at **Ingress**

- Ingress Controller
- Ingress Resources

## Ingress Controller

- Deployment of **Ingress Controller**

## ConfigMap

```
kind: ConfigMap
apiVersion: v1
metadata:
  name: nginx-configuration
```

## Deployment

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-controller
spec:
  replicas: 1
  selector:
    matchLabels:
      name: nginx-ingress
  template:
    metadata:
      labels:
        name: nginx-ingress
    spec:
      serviceAccountName: ingress-serviceaccount
      containers:
        - name: nginx-ingress-controller
          image: quay.io/kubernetes-ingress-controller/nginx-ingress-controller:0.21.0
          args:
            - /nginx-ingress-controller
            - --configmap=$(POD_NAMESPACE)/nginx-configuration
          env:
            - name: POD_NAME
              valueFrom:
                fieldRef:
                  fieldPath: metadata.name
            - name: POD_NAMESPACE
              valueFrom:
                fieldRef:
                  fieldPath: metadata.namespace
          ports:
            - name: http
              containerPort: 80
            - name: https
              containerPort: 443
```

## ServiceAccount

- ServiceAccount require for authentication purposes along with correct Roles, ClusterRoles and RoleBindings.
- Create a ingress service account

```
$ kubectl create -f ingress-sa.yaml
serviceaccount/ingress-serviceaccount created
```

## Service Type - NodePort

```
# service-Nodeport.yaml

apiVersion: v1
kind: Service
metadata:
  name: ingress
spec:
  type: NodePort
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
    name: http
  - port: 443
    targetPort: 443
    protocol: TCP
    name: https
  selector:
    name: nginx-ingress
```

- Create a service

```
$ kubectl create -f service-Nodeport.yaml
```

- To get the service

```
$ kubectl get service
```

## Ingress Resources

```
Ingress-wear.yaml

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-wear
spec:
     backend:
        serviceName: wear-service
        servicePort: 80
```

- To create the ingress resource

```
$ kubectl create -f Ingress-wear.yaml
ingress.extensions/ingress-wear created
```

- To get the ingress

```
$ kubectl get ingress
NAME           CLASS    HOSTS   ADDRESS   PORTS   AGE
ingress-wear   <none>   *                 80      18s
```

## Ingress Resource - Rules

- 1 Rule and 2 Paths.

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-wear-watch
spec:
  rules:
  - http:
      paths:
      - path: /wear
        backend:
          serviceName: wear-service
          servicePort: 80
      - path: /watch
        backend:
          serviceName: watch-service
          servicePort: 80
```

- Describe the earlier created ingress resource

```
$ kubectl describe ingress ingress-wear-watch
Name:             ingress-wear-watch
Namespace:        default
Address:
Default backend:  default-http-backend:80 (<none>)
Rules:
  Host        Path  Backends
  ----        ----  --------
  *
              /wear    wear-service:80 (<none>)
              /watch   watch-service:80 (<none>)
Annotations:  <none>
Events:
  Type    Reason  Age   From                      Message
  ----    ------  ----  ----                      -------
  Normal  CREATE  23s   nginx-ingress-controller  Ingress default/ingress-wear-watch
```

- 2 Rules and 1 Path each.

```
# Ingress-wear-watch.yaml

apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: ingress-wear-watch
spec:
  rules:
  - host: wear.my-online-store.com
    http:
      paths:
      - backend:
          serviceName: wear-service
          servicePort: 80
  - host: watch.my-online-store.com
    http:
      paths:
      - backend:
          serviceName: watch-service
          servicePort: 80
```

# Why Ingress Came into Picture?

Ingress controllers were introduced to address certain limitations and drawbacks of using NodePort or other types of services directly in Kubernetes.

Here are some reasons why Ingress came into the picture:

1. **HTTP/HTTPS Load Balancing**: In Kubernetes, NodePort services can expose applications to the internet, but they often lack features for HTTP/HTTPS load balancing and routing. Ingress controllers provide more advanced routing capabilities, allowing you to route traffic based on URL paths, domain names, headers, etc.
2. **Single Point of Entry**: Ingress controllers allow you to define a single point of entry for multiple services. This is useful for managing multiple applications or microservices within a Kubernetes cluster, providing a more centralized and organized approach to exposing services.
3. **TLS Termination**: While NodePort services can be secured using TLS, managing SSL certificates and termination can be cumbersome. Ingress controllers provide built-in support for TLS termination, simplifying the management of secure connections to your services.
4. **Path-Based Routing**: Ingress controllers allow you to route traffic based on URL paths, enabling more granular control over how requests are directed to different services within your cluster. This is particularly useful for applications with multiple endpoints or APIs.
5. **Dynamic Configuration**: Ingress controllers support dynamic configuration updates, allowing you to modify routing rules and settings without requiring changes to underlying Kubernetes resources. This flexibility makes it easier to adapt to changing requirements and traffic patterns.
6. **Integration with Kubernetes Ecosystem**: Ingress controllers are integrated with the Kubernetes ecosystem, leveraging native resources such as Ingress objects to define routing rules and configurations. This tight integration simplifies deployment and management within Kubernetes environments.

While NodePort services can be used to expose applications externally, they may not provide the same level of flexibility, scalability, and management features as Ingress controllers. Ingress controllers offer a more sophisticated solution for managing inbound traffic to your Kubernetes cluster, especially in production environments with complex routing requirements.

#### References Docs

- https://kubernetes.io/docs/concepts/services-networking/ingress/
- https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/
- https://thenewstack.io/kubernetes-ingress-for-beginners/

