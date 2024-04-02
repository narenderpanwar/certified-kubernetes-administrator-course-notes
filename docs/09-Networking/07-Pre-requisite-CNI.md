## Container Networking Interface (CNI)

### Traditional Container Networking Approach

- Previously, container solutions like Docker, Rocket, and Mesos each had their own way of configuring container networking.
  This involved:

* Network Namespaces: Creating isolated network environments.
* Bridge Networks: Connecting namespaces through a bridge.
* Virtual Interfaces: Creating virtual cables with virtual interfaces on either end.
* Attaching Interfaces: Attaching interfaces to namespaces and the bridge.
* IP Assignment: Assigning IP addresses to interfaces and bringing them up.
* NAT/IP Masquerade: Enabling external communication.
  
  ![CNI](../../images/cni.png)

- But If we are all trying to solve the same networking challenges with our own little minor differences, why code and develop the same solution multiple times?
- This approach led to repetitive work across different container solutions.
- Why not just create a single standard approach that everyone can follow?

### CNI Standardization

- CNI addresses this redundancy by defining a standard for container network plugins.
- So we take all of these ideas from the different solutions and move all the networking portions of it into a single program or code. And since this is for the bridge network, we call it Bridge.
- So we have created a program or script that performs all the required tasks to get the container attached to a bridge network.
  
  ![CNI](../../images/cni1.png)
  **For example**:  You could run this program using its name bridge and specify that you want to add this container to a particular network namespace. The Bridge program takes care of the rest.
  
  ```
  bridge add 2e34dcf34 /var/run/netns/2e34dcf34
  ```
  
  **For example**: When Rocket or Kubernetes creates a new container, they call the Bridge Program and pass the container ID and namespace to get networking configured for that container.
  ![CNI](../../images/cni2.png)

With CNI, any container runtime can work with any compatible plugin.

### Benefits of CNI

* **Reduced Redundancy:** Avoids re-implementing networking solutions for each container runtime.
* **Interoperability:** Enables different runtimes to work with various plugins.

### Existing CNI Plugins

* **Bridge:** Default CNI plugin, creates a simple bridge network.
* **VLAN, IP VLAN, MAC VLAN:** Provides VLAN networking.
* **Windows:** Supports networking for Windows containers.
* **IPAM:** Handles IP address management (e.g., Host Local, DHCP).

### Third-Party CNI Plugins

* Weave
* Flannel
* Cilium
* VMware NSX
* Calico
* Infoblox

### Docker and CNI

Docker uses its own CNM (Container Network Model) standard, which is similar to CNI but with differences. This means CNI plugins don't directly integrate with Docker. However, workarounds exist, such as creating a Docker container without network configuration and then manually invoking the CNI bridge plugin (like Kubernetes does).

# Pre-requisite CNI

- Take me to [Lecture](https://kodekloud.com/topic/prerequsite-cni/)

In this section, we will take a look at **Pre-requisite Container Network Interface(CNI)**

![net-7](../../images/net7.PNG)

## Third Party Network Plugin Providers

- [Weave](https://www.weave.works/docs/net/latest/kubernetes/kube-addon/#-installation)
- [Calico](https://docs.projectcalico.org/getting-started/kubernetes/quickstart)
- [Flannel](https://github.com/coreos/flannel/blob/master/Documentation/kubernetes.md)
- [Cilium](https://github.com/cilium/cilium)

## To view the CNI Network Plugins

- CNI comes with the set of supported network plugins.

```
$ ls /opt/cni/bin/
bridge  dhcp  flannel  host-device  host-local  ipvlan  loopback  macvlan  portmap  ptp  sample  tuning  vlan
```
