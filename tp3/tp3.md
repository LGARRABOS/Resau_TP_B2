# TP3 : On va router des trucs
## I. ARP
### 1. Echange ARP

🌞Générer des requêtes ARP
```
ping 10.3.1.11
PING 10.3.1.11 (10.3.1.11) 56(84) bytes of data.
64 bytes from 10.3.1.11: icmp_seq=1 ttl=64 time=0.582 ms
64 bytes from 10.3.1.11: icmp_seq=2 ttl=64 time=0.935 ms
```
```
ping 10.3.1.12
PING 10.3.1.12 (10.3.1.12) 56(84) bytes of data.
64 bytes from 10.3.1.12: icmp_seq=1 ttl=64 time=0.644 ms
64 bytes from 10.3.1.12: icmp_seq=2 ttl=64 time=0.889 ms
```
---

Mac de Jhon dans Marcel:``` 10.3.1.11 dev enp0s3 lladdr 08:00:27:2a:92:79 STALE```
Mac de Marcel dans Jhon: ``` 10.3.1.12 dev enp0s3 lladdr 08:00:27:44:7d:ed STALE ```

---

Mac de Marcel dans Marcel: ``` 08:00:27:44:7d:ed```

### 2. Analyse de trames

🌞Analyse de trames

---


---

## II. Routage
### 1. Mise en place du routage

🌞Activer le routage sur le noeud router

```
[luc@localhost ~]$ sudo firewall-cmd --add-masquerade --zone=public --permanent
success
```
🌞Ajouter les routes statiques nécessaires pour que john et marcel puissent se ping

```
sudo nano /etc/sysconfig/network-scripts/route-enp0s3

10.3.1.11 via 103.2.254 dev enp0s3
```
```
[luc@Marcel ~]$ ping 10.3.1.11
PING 10.3.1.11 (10.3.1.11) 56(84) bytes of data.
64 bytes from 10.3.1.11: icmp_seq=1 ttl=63 time=1.13 ms
64 bytes from 10.3.1.11: icmp_seq=2 ttl=63 time=1.82 ms
64 bytes from 10.3.1.11: icmp_seq=3 ttl=63 time=1.84 ms
```

### 2. Analyse de trames

```
sudo ip neigh flush all
```
```
[luc@John ~]$ ping 10.3.2.12
PING 10.3.2.12 (10.3.2.12) 56(84) bytes of data.
64 bytes from 10.3.2.12: icmp_seq=1 ttl=63 time=2.14 ms
64 bytes from 10.3.2.12: icmp_seq=2 ttl=63 time=1.71 ms
64 bytes from 10.3.2.12: icmp_seq=3 ttl=63 time=1.84 ms
64 bytes from 10.3.2.12: icmp_seq=4 ttl=63 time=2.05 ms
```
Source:PcsCompu_46:aa:c0; Destination: Broadcast; Protocol: ARP;

Source:PcsCompu_44:7d:ed; Destination: PcsCompu_46:aa:c0; Protocol: ARP;

Source:10.3.2.254; Destination: 10.3.2.12; Protocol: ICMP;

Source:10.3.2.12; Destination: 10.3.2.254; Protocol: ICMP;


---

---

### 3. Accès internet

🌞Donnez un accès internet à vos machines

```
[luc@routeur ~]$ ip a

enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:10:25:ff brd ff:ff:ff:ff:ff:ff
    inet 10.0.4.15/24 brd 10.0.4.255 scope global dynamic noprefixroute enp0s9
       valid_lft 86359sec preferred_lft 86359sec
    inet6 fe80::abac:2e37:10fd:4568/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```

Route par défaut Jhon: 
```
[luc@John ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=246 time=20.2 ms
```

Route par défaut Marcel:
```
[luc@Marcel ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=246 time=37.0 ms
```

Utilisation de dig: 
```
[luc@John ~]$ dig google.com

; <<>> DiG 9.16.23-RH <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 54933
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             135     IN      A       142.250.75.238

;; Query time: 29 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Wed Oct 12 09:06:30 CEST 2022
;; MSG SIZE  rcvd: 55
```

ping vers un nom de domaine:
```
[luc@John ~]$ ping google.com
PING google.com (216.58.201.238) 56(84) bytes of data.
64 bytes from fra02s18-in-f14.1e100.net (216.58.201.238): icmp_seq=1 ttl=246 time=36.4 ms
```

🌞Analyse de trames

|ordre| type trame| IP source | MAC source | IP destination| MAC destination|

| 1 | ICMP |10.3.1.11 |08:00:27:5a:9c:1e|8.8.8.8 |08:00:27:6a:a4:23|

| 2 | ICMP |8.8.8.8 |08:00:27:6a:a4:23|10.3.1.11 |08:00:27:5a:9c:1e|

## II. DHCP
### 1. Mise en place du serveur DHCP

🌞Sur la machine john, vous installerez et configurerez un serveur DHCP

```
[luc@John ~]$ sudo dnf install dhcp-server -y


Installed:
  dhcp-common-12:4.4.2-15.b1.el9.noarch                      dhcp-server-12:4.4.2-15.b1.el9.x86_64

Complete!
```
```
[luc@John ~]$ sudo cat /etc/dhcp/dhcpd.conf
#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp-server/dhcpd.conf.example
#   see dhcpd.conf(5) man page
#


default-lease-time 900;
max-lease-time 10800;
ddns-update-style none;
authoritative;
subnet 10.3.1.0 netmask 255.255.255.0 {
  range 10.3.1.12 10.3.1.250;
  option subnet-mask 255.255.255.0;

}
```
```
[luc@John ~]$ sudo firewall-cmd --add-port=67/udp --permanent
success
```
```
[luc@John ~]$ sudo systemctl status dhcpd
● dhcpd.service - DHCPv4 Server Daemon
     Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; enabled; vendor preset: disabled)
     Active: active (running) since Wed 2022-10-12 09:24:45 CEST; 7s ago
       Docs: man:dhcpd(8)
             man:dhcpd.conf(5)
   Main PID: 10563 (dhcpd)
     Status: "Dispatching packets..."
      Tasks: 1 (limit: 5907)
     Memory: 5.3M
        CPU: 27ms
     CGroup: /system.slice/dhcpd.service
             └─10563 /usr/sbin/dhcpd -f -cf /etc/dhcp/dhcpd.conf -user dhcpd -group dhcpd --no-pid
```

Récupérer une IP pour bob: 

```
[luc@bob ~]$ cat /etc/sysconfig/network-scripts/ifcfg-enp0s3
NAME=enp0s3
DEVICE=enp0s3

BOOTPROTO=dhcp
ONBOOT=yes
```
```
[luc@bob ~]$ ip a

2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:dd:31:ba brd ff:ff:ff:ff:ff:ff
    inet 10.3.1.12/24 brd 10.3.1.255 scope global dynamic noprefixroute enp0s3
       valid_lft 775sec preferred_lft 775sec
    inet6 fe80::a00:27ff:fedd:31ba/64 scope link
       valid_lft forever preferred_lft forever
```

🌞Améliorer la configuration du DHCP

```
[luc@John ~]$ sudo cat /etc/dhcp/dhcpd.conf
[...]
  option routers 10.3.1.254;
  option domain-name-servers 8.8.8.8;
}
```
Récupérez de nouveau une IP en DHCP sur Bob pour tester:
```
[luc@bob ~]$ sudo ip addr del 10.3.1.12/24 dev enp0s3
```
```
[luc@bob ~]$ ip a

2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:dd:31:ba brd ff:ff:ff:ff:ff:ff
    inet 10.3.1.12/24 brd 10.3.1.255 scope global dynamic noprefixroute enp0s3
       valid_lft 848sec preferred_lft 848sec
    inet6 fe80::a00:27ff:fedd:31ba/64 scope link
       valid_lft forever preferred_lft forever
```
Ping de la passerelle:
```
[luc@bob ~]$ ping 10.3.1.254
PING 10.3.1.254 (10.3.1.254) 56(84) bytes of data.
64 bytes from 10.3.1.254: icmp_seq=1 ttl=64 time=1.14 ms
```
Vérifier la route:
```
[luc@bob ~]$ ip route show
default via 10.3.1.254 dev enp0s3 proto dhcp src 10.3.1.12 metric 100
10.3.1.0/24 dev enp0s3 proto kernel scope link src 10.3.1.12 metric 100
```
Vérifier avec dig:
```
[luc@bob ~]$ dig google.com

; <<>> DiG 9.16.23-RH <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 28161
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             202     IN      A       142.250.179.78

```
Ping un nom de domaine:
```
[luc@bob ~]$ ping google.com
PING google.com (142.250.179.78) 56(84) bytes of data.
64 bytes from par21s19-in-f14.1e100.net (142.250.179.78): icmp_seq=1 ttl=114 time=15.4 ms
```