# Pre-requisite Switching Routing Gateways

This lecture series covers basic networking concepts, including switching, routing, gateways, DNS, CoreDNS, and network namespaces in Linux. It is intended for system administrators and application developers who need to understand networking fundamentals but may not be network engineers.

## Switching

* So what is a `Network`?
  
  - We have two computers, A and B, laptops, desktops, VMs on the cloud, wherever, how does system A reach and communicate to B?
  - We connect them to a `Switch`and the`Switch creates a network` containing the two systems.
  - To connect the two systems to a switch, we need an `Interface` (eth0) on each host: physical or virtual, depending on the host. To see the interfaces for the host, we use the IP link command.
    
    ![switch](../../images/switch.png)
  - To see the interfaces for the host
    
    ```
    $ ip link
    ```
  - Let's assume the Switch creates a network with the address 192.168.1.0
  - We then assign the systems with IP addresses on the same network. For this, we use the command ip addr.
    
    ```
    $ ip addr add 192.168.1.10/24 dev eth0  [System A]
    ```
    
    ```
    $ ip addr add 192.168.1.11/24 dev eth0  [System B]
    ```
  - Once the links are up and the IP addresses are assigned, the two systems A and B can now communicate with each other through the switch. The switch can only enable communication within a network, which means it can receive packets from a host on the network and deliver it to other systems within the same network.
    ![net-14](../../images/net14.PNG)

## Routing

- But what if we have another network containing systems C and D at address 192.168.2.0 and the systems have IP address 192.168.2.10 and 198.168.2.11 respectively. `How does a system in one network reach a system in the other network?`
  
  ![net-14](../../images/switch2.png)

* That's where **`Router`** comes in, a router connects two or more networks together. Think of it as another server with many network ports.
* Since it connects to the two separate networks, it gets two IPs assigned, one on each network. In the first network, we assign it an IP address 192.168.1.1 And in the second, we assign it an IP 192.168.2.1
  
  ![net-14](../../images/switch3.png)

- To see the existing routing table on the host system.
  
  ```
  $ route
  ```
  
  ```
  $ ip route show
  or
  $ ip route list
  ```
- To add entries into the routing table.
  
  ```
  $ ip route add 192.168.1.0/24 via 192.168.2.1
  ```

## Gateway

- When system B tries to send a packet to system C, how does it know where the router is on the network to send the packet through? The router is just another device on the network. There could be many other such devices. That's where we configure the systems with a `Gateway`
  *Analogy* : If the Network is a room, then the gateway is a door to the outside world or to the other networks or to the internet. The systems need to know where that door is to go through that.
- To see the existing routing table on the host system.
  
  ```
  $ route
  ```
  
  ![net-14](../../images/gw.png)
- As you can see, there are no routing configurations as of now. Hence, system B will not be able to reach system C.
- To configure a gateway on system B to reach the systems on network 192.168.2.0, run the IP route add command and specify that you can reach the 192.168.2.0 network through the door or gateway at 192.168.1.1
  
  ```
  $ ip route add 192.168.2.0/24 via 192.168.1.1
  ```
  
  ![net-14](../../images/gw1.png)

- To add a default route.

```
$ ip route add default via 192.168.2.1
```

- To check the IP forwarding is enabled on the host.

```
$ cat /proc/sys/net/ipv4/ip_forward
0

$ echo 1 > /proc/sys/net/ipv4/ip_forward
```

- Enable packet forwarding for IPv4.

```
$ cat /etc/sysctl.conf

# Uncomment the line
net.ipv4.ip_forward=1
```

- To view the sysctl variables.

```
$ sysctl -a
```

- To reload the sysctl configuration.

```
$ sysctl --system
```

## Configuring a Host as a Router

* A Linux host can be configured as a router to enable communication between two networks.
* This is done by adding routing entries to the routing tables of the hosts on the networks.
* Additionally, IP forwarding must be enabled on the host that is acting as the router.

## IP Forwarding

* By default, IP forwarding is disabled on Linux hosts.
* This is for security reasons to prevent unauthorized communication between networks.
* To enable IP forwarding, the value of the `net.ipv4.ip_forward` file in `/proc/sys/net/ipv4` must be set to 1.
* To make the change persistent across reboots, the value must also be set to 1 in the `/etc/sysctl.conf` file.

## Commands

* `ip link`: Lists and modifies interfaces on a host.
* `ip addr`: Shows the IP addresses assigned to interfaces.
* `ip addr add`: Sets IP addresses on interfaces. (Changes are not persistent until reboot.)
* `ip route` or `route`: Views the routing table.
* `ip route add`: Adds entries to the routing table.
* `sysctl net.ipv4.ip_forward`: Checks if IP forwarding is enabled.

