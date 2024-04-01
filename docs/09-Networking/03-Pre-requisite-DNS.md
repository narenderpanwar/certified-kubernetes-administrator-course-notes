## DNS

- Every host has a DNS resolution configuration file at `/etc/resolv.conf`.

```
$ cat /etc/resolv.conf
nameserver 127.0.0.53
options edns0
```

- To change the order of dns resolution, we need to do changes into the `/etc/nsswitch.conf` file.

```
$ cat /etc/nsswitch.conf

hosts:          files dns
networks:       files
```

- If it fails in some conditions.

```
$ ping wwww.github.com
ping: www.github.com: Temporary failure in name resolution
```

- Adding well known public nameserver in the `/etc/resolv.conf` file.

```
$ cat /etc/resolv.conf
nameserver   127.0.0.53
nameserver   8.8.8.8
options edns0
```

```
$ ping www.github.com
PING github.com (140.82.121.3) 56(84) bytes of data.
64 bytes from 140.82.121.3 (140.82.121.3): icmp_seq=1 ttl=57 time=7.07 ms
64 bytes from 140.82.121.3 (140.82.121.3): icmp_seq=2 ttl=57 time=5.42 ms
```

## Domain Names

![net-8](../../images/net8.PNG)

## Search Domain

![net-9](../../images/net9.PNG)

## Record Types

![net-10](../../images/net10.PNG)

## Networking Tools

- Useful networking tools to test dns name resolution.

#### nslookup

```
$ nslookup www.google.com
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
Name:   www.google.com
Address: 172.217.18.4
Name:   www.google.com
```

#### dig

```
$ dig www.google.com

; <<>> DiG 9.11.3-1 ...
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 8738
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;www.google.com.                        IN      A

;; ANSWER SECTION:
www.google.com.         63      IN      A       216.58.206.4

;; Query time: 6 msec
;; SERVER: 127.0.0.53#53(127.0.0.53)
```

# Pre-requisite DNS

- Take me to [Lecture](https://kodekloud.com/topic/prerequsite-dns/)

In this section, we will take a look at **DNS in the Linux**

## Name Resolution

- With help of the `ping` command. We can check the reachability of the IP Address on the Network.
  
  ```
  $ ping 172.17.0.64
  PING 172.17.0.64 (172.17.0.64) 56(84) bytes of data.
  64 bytes from 172.17.0.64: icmp_seq=1 ttl=64 time=0.384 ms
  64 bytes from 172.17.0.64: icmp_seq=2 ttl=64 time=0.415 ms
  ```

# DNS

1. **Setting up Hostnames and IP Address Mapping:**
   
   - Two computers, A and B, have been assigned IP addresses 192.168.1.10 and 192.168.1.11 respectively. You are able to ping other computer using it's IP address.
     
     ![net-10](../../images/dns.png)
   - Instead of having to remember the IP address of system B, you decide to give it a name db.
   - If you try to ping `db` now, you would see that host A is unaware of a host named db.
     So how do you fix that?
   - To resolve this, an entry is to be added to the `/etc/hosts` file on host A, mapping the hostname 'db' to the IP address 192.168.1.11.
     ![net-10](../../images/dns1.png)
2. **Hostname Resolution and `/etc/hosts` File:**
   
   - Host A relies on the `/etc/hosts` file for name resolution.
   - Entries in this `/etc/hosts` serve as the source of truth for hostname-to-IP mappings on the local system, regardless of the actual hostname of remote systems.
   - Multiple names can point to the same system; for example, host A can resolve both 'db' and 'www.google.com' to the IP address of system B.
     
     ![net-10](../../images/dns2.png)
3. **Limitations of Local Host Configuration:**
   
   - Within a small network of few systems, you can easily get away with the entries in the /etc/hosts file. On each system, you can specify which are the other systems in the network, and that's how it was done in the past.
     
     ![net-10](../../images/dns3.png)
   - However managing numerous entries in the `/etc/hosts` file becomes cumbersome as the network grows. `If one of the servers IP gets changed, you have to modify the entries in all of the hosts' /etc/hosts file.`
     ![net-10](../../images/dns4.png)
4. **Introduction of DNS Server:**
   
   - To centralize hostname resolution, a DNS server is introduced to manage all the enrties centrally. We then point all hosts to look up for that server if they need to resolve a host name to an IP address instead of its own /etc/hosts files.
   - `How do we point our host to a DNS server?`
     ![net-10](../../images/dns5.png)
   - Every host has a DNS resolution configuration file at `/etc/resolv.conf`. You add an entry into it specifying the IP address of the DNS server. We say `nameserver` and point it to our DNS Server i.e `192.168.1.100`, and that should be it. Once this is configured on all of your hosts, every time a host comes up across a host name that it does not know about, it looks it up from the DNS server.
     
     ![net-10](../../images/dns6.png)
5. **DNS Resolution Process:**
   
   - With DNS configured, hosts query the DNS server for hostname resolution.
   - Updates to IP addresses are managed centrally on the DNS server, simplifying maintenance.
6. **Combining Local and DNS Resolution:**
   
   - While DNS is the primary method for hostname resolution, local `/etc/hosts` entries can still be utilized for specific cases, such as test servers.
7. **Order of Resolution:**
   
   - The order of hostname resolution is defined in the `/etc/nsswitch.conf` file.
   - By default, it first checks the `/etc/hosts` file (`files`) and then queries the DNS server (`dns`).
8. **Fallback for Unknown Hosts:**
   
   - Hosts query both local and DNS sources for hostname resolution.
   - If a hostname is not found in either source, the resolution fails.
9. **Fallback DNS Servers:**
   
   - Additional DNS servers, such as public servers like 8.8.8.8, can be added to `/etc/resolv.conf` to resolve external hosts not present in the local DNS.
10. **Configuring DNS Forwarding:**
    
    - Internal DNS servers can be configured to forward unresolved queries to external DNS servers, ensuring comprehensive hostname resolution.
11. **Limitations of External Access:**
    
    - Access to external sites like facebook.com may be restricted, depending on network configurations.
12. **Conclusion:**
    
    - Hostname resolution involves a combination of local configuration (`/etc/hosts`) and centralized DNS servers.
    - DNS simplifies management and scalability but requires proper configuration for external access.
    - Local configurations can be used for specific cases not covered by DNS.

