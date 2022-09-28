# TP1 - Mise en jambes
## I. Exploration locale en solo
### 1. Affichage d'informations sur la pile TCP/IP locale

🌞 Affichez les infos des cartes réseau de votre PC
```
PS C:\Users\garra> ipconfig /all

Configuration IP de Windows

Carte Ethernet Ethernet :
Adresse physique . . . . . . . . . . . : E4-A8-DF-FF-9D-74
```
Pas d'adresse IPV4 sur la carte Ethernet car non connecter au résau.
```
Carte réseau sans fil Wi-Fi :
Adresse physique . . . . . . . . . . . : 74-4C-A1-46-01-65
Adresse IPv4. . . . . . . . . . . . . .: 10.33.19.76(préféré)
   ```
---
🌞 Affichez votre gateway
```
PS C:\Users\garra> ipconfig /all

Configuration IP de Windows

Passerelle par défaut. . . . . . . . . : 10.33.19.254
```
---
🌞 Trouvez comment afficher les informations sur une carte IP (change selon l'OS)
![](https://i.imgur.com/xvfPL21.png)
---
Dans le résau d'Ynov la Gateway permet aux utilisateur du résau local d'Ynov de communiquer avec l'extérieur.

### 2. Modifications des informations
#### A. Modification d'adresse IP (part 1)

🌞 Utilisez l'interface graphique de votre OS pour changer d'adresse IP :
![](https://i.imgur.com/L4pwyWN.png)

Pour ma part j'ai perdu Internet car il est probbable que l'adresse IP que j'ai choisie soit déja attribuer à un autre appareil du résau.

## II. Exploration locale en duo

### 3. Modification d'adresse IP

- Si vos PCs ont un port RJ45 alors y'a une carte réseau Ethernet associée
```
PS C:\Users\xouxo> ping 192.168.0.1

Envoi d’une requête 'Ping'  192.168.0.1 avec 32 octets de données :
Réponse de 192.168.0.1 : octets=32 temps<1ms TTL=128
Réponse de 192.168.0.1 : octets=32 temps<1ms TTL=128
Réponse de 192.168.0.1 : octets=32 temps<1ms TTL=128
Réponse de 192.168.0.1 : octets=32 temps<1ms TTL=128

Statistiques Ping pour 192.168.0.1:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 0ms, Maximum = 0ms, Moyenne = 0ms
```
```
PS C:\Users\xouxo> arp -a
[...]
Interface : 192.168.0.2 --- 0xa
  Adresse Internet      Adresse physique      Type
  192.168.0.1           e4-a8-df-ff-9d-74     dynamique
  192.168.0.3           ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  224.0.0.252           01-00-5e-00-00-fc     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique
```

### 4. Utilisation d'un des deux comme gateway

- utiliser un traceroute ou tracert pour bien voir que les requêtes passent par la passerelle choisie (l'autre le PC)

```
PS C:\Users\garra> tracert  -4 8.8.8.8

Détermination de l’itinéraire vers dns.google [8.8.8.8]
avec un maximum de 30 sauts :

  1     2 ms     *        2 ms  LAPTOP-UORD0VVO [192.168.137.1]
  2     *        *        *     Délai d’attente de la demande dépassé.
  3     4 ms     4 ms     3 ms  10.33.19.254
  4     8 ms     8 ms     7 ms  137.149.196.77.rev.sfr.net [77.196.149.137]
  5    11 ms    16 ms    12 ms  108.97.30.212.rev.sfr.net [212.30.97.108]
  6    22 ms    22 ms    22 ms  222.172.136.77.rev.sfr.net [77.136.172.222]
  7    22 ms    24 ms    23 ms  221.172.136.77.rev.sfr.net [77.136.172.221]
  8    24 ms    27 ms    28 ms  186.144.6.194.rev.sfr.net [194.6.144.186]
  9    25 ms    25 ms    24 ms  186.144.6.194.rev.sfr.net [194.6.144.186]
 10    23 ms    23 ms    25 ms  72.14.194.30
 11    27 ms    26 ms    25 ms  172.253.69.49
 12    25 ms    25 ms    25 ms  108.170.238.107
 13    26 ms    25 ms    26 ms  dns.google [8.8.8.8]

Itinéraire déterminé.
```

### 5. Petit chat privé

- sur le PC serveur avec par exemple l'IP 192.168.1.1


```
PS C:\Users\xouxo\Desktop\netcat-1.11> ./nc.exe -l -p 8888
fglfjgfgdxxcc vvbvvvcvvvvvv
cvc
v
v
v
vv
v
 v
Salut
COomment sa vas
```

- sur le PC client avec par exemple l'IP 192.168.1.2

```
PS C:\Users\garra\Desktop\netcat-1.11> ./nc.exe 192.168.0.2 8888
Salut
fglfjgfgdxxcc vvbvvvcvvvvvv
cvc
v
v
v
vv
v
 v
COomment sa vas
```

- pour aller un peu plus loin

```
PS C:\Users\xouxo\Desktop\netcat-1.11>./nc.exe -l -p 8888 192.168.0.1
HOLLA
COMO ALLEZ VU
UI
UI
UWU
UW
U
W
UWU
PS C:\Users\xouxo\Desktop\netcat-1.11> ./nc.exe -l -p 8888 192.168.0.4
invalid connection to [192.168.0.2] from (UNKNOWN) [192.168.0.1] 50903
```

### 6. Firewall

- Autoriser les ping

```
PS C:\Users\xouxo> netsh advfirewall firewall add rule name="ICMP Allow incoming V4 echo request" protocol=icmpv4:8,any dir=in action=allow
Ok.
```

- Autoriser le traffic sur le port qu'utilise nc
```
PS C:\Users\xouxo> netsh advfirewall firewall add rule name="ALLOW TCP PORT 1050" dir=in action=allow protocol=TCP localport=1050
Ok.

PS C:\Users\xouxo> cd .\Desktop\netcat-1.11\
PS C:\Users\xouxo\Desktop\netcat-1.11> .\nc.exe -l -p 1050
dd
d
d
d
d
d
```


## III. Manipulations d'autres outils/protocoles côté client
### 1. DHCP

🌞Exploration du DHCP, depuis votre PC

```
PS C:\Users\garra> ipconfig /all

Configuration IP de Windows

Carte réseau sans fil Wi-Fi :

Serveur DHCP . . . . . . . . . . . . . : 10.33.19.254

Bail obtenu. . . . . . . . . . . . . . : mercredi 28 septembre 2022 08:21:49
Bail expirant. . . . . . . . . . . . . : jeudi 29 septembre 2022 08:21:49
```

### 2. DNS
🌞 trouver l'adresse IP du serveur DNS que connaît votre ordinateur

```
PS C:\Users\garra> ipconfig /all

Configuration IP de Windows

Carte réseau sans fil Wi-Fi :

Serveurs DNS. . .  . . . . . . . . . . : 8.8.8.8
                                       8.8.4.4
                                       1.1.1.1
```
🌞 utiliser, en ligne de commande l'outil nslookup (Windows, MacOS) ou dig (GNU/Linux, MacOS) pour faire des requêtes DNS à la main
```
PS C:\Users\garra\Desktop\netcat-1.11> nslookup.exe google.com

Nom :    google.com
Addresses:  2a00:1450:4007:808::200e
          216.58.215.46
```
L'ip de google est 216.58.215.46
```
PS C:\Users\garra\Desktop\netcat-1.11> nslookup.exe ynov.com

Nom :    ynov.com
Addresses:  2606:4700:20::681a:be9
          2606:4700:20::681a:ae9
          2606:4700:20::ac43:4ae2
          104.26.10.233
          104.26.11.233
          172.67.74.226
```
Ynov possédent plusieurs IP et donc probabelement plusieurs serveurs.
```
PS C:\Users\garra\Desktop\netcat-1.11> nslookup 78.74.21.21


Nom :    host-78-74-21-21.homerun.telia.com
Address:  78.74.21.21
```
```
PS C:\Users\garra\Desktop\netcat-1.11> nslookup 92.146.54.88


*** dns.google ne parvient pas à trouver 92.146.54.88 : Non-existent domain
```

## IV. Wireshark

![](https://i.imgur.com/5JCQxfT.png)

---

![](https://i.imgur.com/YECf5Ij.png)

---

