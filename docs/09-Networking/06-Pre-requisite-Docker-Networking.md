# Networking in Docker

- **Introduction to Docker Networking:**
  
  - Docker allows various networking options for containers.
  - This lecture focuses on basic networking options and relates them to networking namespaces.
- **Single Docker Host Setup:**
  
  - A Docker host is a server with Docker installed.
  - It has an internet interface at port 80 connecting to the local network with IP address 192.168.1.10.
- **Networking Options:**
  
  1. **None Network:**
     
     - Container isn't attached to any network.
     - No connectivity with the outside world or between containers.
     
     ![DN](../../images/dn.png)
  2. **Host Network:**
     
     - Container shares host network.
     - No network isolation between host and container.
     - Applications in containers are directly available on host ports.
     - Can't run multiple containers listening on the same port simultaneously.
     
     ![DN](../../images/dn1.png)
  3. **Bridge Network:**
     
     - Creates an internal private network.
     - Default address: 172.17.0.0.
     - Each device gets a unique internal private network address.
     
     ![DN](../../images/dn2.png)
- **Understanding Docker's Bridge Network:**
  
  - Docker creates an internal private network named "Bridge". To check :
    
    ```
    docker network ls
    ```
  - Docker calls the network by the name Bridge, but on the host, the network is created by the name `Docker0`. To check:
    
    ```
    ip link
    ```
    
    ![DN](../../images/dn3.png)
  - Similar technique to namespaces using `IP link add` command with type set to bridge.
  - Remember, we said that the bridge network is like an interface to the host but a switch to the namespaces or containers within the host. So the interface docker0 on the host is assigned an IP 172.17.0.1.
    ![DN](../../images/dn4.png)
  - Docker creates a network namespace for each container.
    ![DN](../../images/dn5.png)
- **Container Attachment to Bridge Network:**
  
  - Docker attaches containers to the bridge network via a virtual cable with two interfaces.
  - One end attached to local bridge Docker0 on the host and another end within the container namespace.
  - Each container/network namespace gets an IP address within the network.
    
    ![DN](../../images/dn6.png)
- **Port Mapping:**
  
  - Example with Nginx container serving webpage on port 80.
  - Containers within the private network or host can access the webpage.
  - External users require port publishing or mapping.
  - Mapping port 8080 on Docker host to port 80 on the container.
  - Allows access to the application using Docker host's IP and port 8080.
- **Forwarding Traffic:**
  
  - Docker forwards traffic from one port to another using NAT rules.
  - Utilizes IP tables to create NAT rules.
  - Adds rules to Docker chain to change destination port.
  - Docker rule includes the container's IP as well.
- **Conclusion:**
  
  - Docker provides various networking options including none, host, and bridge networks.
  - Understanding Docker networking is crucial for managing containerized applications effectively.

# Pre-requisite Docker Networking

- Take me to [Lecture](https://kodekloud.com/topic/prerequsite-docker-networking/)

In this section, we will take a look at **Docker Networking**


## To view the Network Device on the Host

```
$ ip link
or
$ ip link show docker0
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN mode DEFAULT group default
    link/ether 02:42:cf:c3:df:f5 brd ff:ff:ff:ff:ff:ff
```

- With the help of `ip link add` command to type set `bridge` to `docker0`

```
$ ip link add docker0 type bridge
```

## To view the IP Addr of the interface docker0

```
$ ip addr
or
$ ip addr show docker0
3: docker0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default
    link/ether 02:42:cf:c3:df:f5 brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.1/24 brd 172.18.0.255 scope global docker0
       valid_lft forever preferred_lft forever
```

## Run the command to create a Docker Container

```
$ docker run nginx
```

## To list the Network Namespace

```
$ ip netns
1c452d473e2a (id: 2)
db732004aa9b (id: 1)
04acb487a641 (id: 0)
default

# Inspect the Docker Container

$ docker inspect <container-id>

# To view the interface attached with the local bridge docker0

$ ip link
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default
    link/ether 02:42:c8:3a:ea:67 brd ff:ff:ff:ff:ff:ff
5: vetha3e33331@if3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP mode DEFAULT group default
    link/ether e2:b2:ad:c9:8b:98 brd ff:ff:ff:ff:ff:ff link-netnsid 0

# with -n options with the network namespace to view the other end of the interface

$ ip -n 04acb487a641 link
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN mode DEFAULT group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
3: eth0@if5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP mode DEFAULT group default
    link/ether c6:f3:ca:12:5e:74 brd ff:ff:ff:ff:ff:ff link-netnsid 0

# To view the IP Addr assigned to this interface 

$ ip -n 04acb487a641 addr
3: eth0@if5: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether c6:f3:ca:12:5e:74 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.244.0.2/24 scope global eth0
       valid_lft forever preferred_lft forever
```

## Port Mapping

- Creating a docker container.

```
$ docker run -itd --name nginx nginx
d74ca9d57c1d8983db2c590df2fdd109e07e1972d6b361a6ecad8a942af5bf7e
```

- Inspect the docker container to view the IPAddress.

```
$ docker inspect nginx | grep -w IPAddress
            "IPAddress": "172.18.0.6",
                    "IPAddress": "172.18.0.6",
```

- Accessing web page with the `curl` command.

```
$ curl --head  http://172.18.0.6:80
HTTP/1.1 200 OK
Server: nginx/1.19.2
```

- Port Mapping to docker container

```
$ docker run -itd --name nginx -p 8080:80 nginx
e7387bbb2e2b6cc1d2096a080445a6b83f2faeb30be74c41741fe7891402f6b6
```

- Inspecting docker container to view the assgined ports.

```
$ docker inspect nginx | grep -w -A5 Ports

  "Ports": {
                "80/tcp": [
                    {
                        "HostIp": "0.0.0.0",
                        "HostPort": "8080"
                    }
```

- To view the IP Addr of the host system

```
$ ip a

# Accessing nginx page with curl command

$ curl --head http://192.168.10.11:8080
HTTP/1.1 200 OK
Server: nginx/1.19.2
```

- Configuring **iptables nat** rules

```
$ iptables \
         -t nat \
         -A PREROUTING \
         -j DNAT \
         --dport 8080 \
         --to-destination 80
```

```
$ iptables \
      -t nat \
      -A DOCKER \
      -j DNAT \
      --dport 8080 \
      --to-destination 172.18.0.6:80
```

## List the Iptables rules

```
$ iptables -nvL -t nat
```

#### References docs

- https://docs.docker.com/network/
- https://linux.die.net/man/8/iptables
- https://linux.die.net/man/8/ip

