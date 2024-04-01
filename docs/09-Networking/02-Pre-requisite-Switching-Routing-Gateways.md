# Pre-requisite Switching Routing Gateways

This lecture series covers basic networking concepts, including switching, routing, gateways, DNS, CoreDNS, and network namespaces in Linux. It is intended for system administrators and application developers who need to understand networking fundamentals but may not be network engineers.

## Switching

* A network is a group of computers connected together.
* Computers can communicate with each other on the same network through a switch.
* A switch is an intelligent device that forwards packets to the correct destination within the network.

- To see the interface on the host system

```
$ ip link
```

- To see the IP Address interfaces.

```
$ ip addr
```

![net-14](../../images/net14.PNG)

## Routing

* A router connects two or more networks together.
* When a computer on one network needs to communicate with a computer on another network, the packets are sent to the router.
* The router then forwards the packets to the appropriate network.

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

![net-15](../../images/net15.PNG)

## Gateways

* A gateway is the IP address of the router on a network.
* Computers on a network need to be configured with the correct gateway address in order to communicate with computers on other networks.

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

