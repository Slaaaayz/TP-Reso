# TP3 : On va router des trucs

## 1.Echange ARP
**🌞Générer des requêtes ARP**
- effectuer un ping d'une machine à l'autre
```powershell
ping 10.3.1.11
```
**Réponse :**
```powershell
[slayz@localhost ~]$ ping 10.3.1.11
PING 10.3.1.11 (10.3.1.11) 56(84) bytes of data.
64 bytes from 10.3.1.11: icmp_seq=1 ttl=64 time=0.421 ms
64 bytes from 10.3.1.11: icmp_seq=2 ttl=64 time=0.923 ms
64 bytes from 10.3.1.11: icmp_seq=3 

--- 10.3.1.11 ping statistics ---
15 packets transmitted, 15 received, 0% packet loss, time 14039ms
rtt min/avg/max/mdev = 0.421/0.943/1.274/0.165 ms
```
Puis 
```powerhsell
ping 10.3.1.12
```
**Réponse :**
```powershell
[slayz@localhost ~]$ ping 10.3.1.12
PING 10.3.1.12 (10.3.1.12) 56(84) bytes of data.
64 bytes from 10.3.1.12: icmp_seq=1 ttl=64 time=1.10 ms
64 bytes from 10.3.1.12: icmp_seq=2 ttl=64 time=1.09 ms
64 bytes from 10.3.1.12: icmp_seq=3 ttl=64 time=0.942 ms

--- 10.3.1.12 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3007ms
rtt min/avg/max/mdev = 0.932/1.014/1.097/0.077 ms
```
- observer les tables ARP des deux machines

```powershell
ip n s 
```
**Réponse :**
```powershell
[slayz@localhost ~]$ ip n s
10.3.1.11 dev enp0s3 lladdr 08:00:27:b2:92:c0 STALE
```
et 
```powershell
[slayz@localhost ~]$ ip n s
10.3.1.12 dev enp0s3 lladdr 08:00:27:d3:e6:77 STALE
```
- repérer l'adresse MAC de john dans la table ARP de marcel et vice-versa

Addresse MAC :
- John : 08:00:27:b2:92:c0
- Marcel : 08:00:27:d3:e6:77

- prouvez que l'info est correcte (que l'adresse MAC que vous voyez dans la table est bien celle de la machine correspondante)

  - une commande pour voir la MAC de marcel dans la table ARP de john  
  ```bash
  ip n s 10.3.1.12
  ```
**Réponse :**
```bash
[slayz@localhost ~]$ ip n s 10.3.1.12
10.3.1.12 dev enp0s3 lladdr 08:00:27:d3:e6:77 STALE
```
  - et une commande pour afficher la MAC de marcel, depuis marcel  
  ```bash
  ip a
  ```
  **Réponse :**
  ```bash
  2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:d3:e6:77 brd ff:ff:ff:ff:ff:ff
    inet 10.3.1.12/24 brd 10.3.1.255 scope global noprefixroute enp0s3
```

## 2. Analyse de trames

**🌞Analyse de trames**  

**Commande à utiliser  :**

- Commande pour vider la table ARP :
```bash
[slayz@localhost ~]$ sudo ip neigh flush all
```
- Commande pour commencer une capture tcpdump : 
```bash
[slayz@localhost ~]$ sudo tcpdump -i enp0s3 -c 30 -w tp3_arp.pcap
```
- Commande pour ping :
```bash
[slayz@localhost ~]$ ping 10.3.1.11
```
- Commande pour récuperer le .pcapng sur windows :
```bash
C:\Users\melb3>scp slayz@10.3.1.11:/home/slayz/arplinux.pcap ./
```
[Clique ici pour voir les trames ARP ](./tp3_arp.pcapng)

# II. Routage
## 1. Mise en place du routage 

**🌞Ajouter les routes statiques nécessaires pour que john et marcel puissent se ping**  
- Commande pour ajouter les routes statiques :
    - Pour marcel :
```bash
sudo ip route add 10.3.1.0/24 via 10.3.2.254 dev enp0s3
```
Add route definitif :
```bash
[slayz@localhost ~]$ sudo nano /etc/sysconfig/network-scripts/route-enp0s3

10.3.1.0 via 10.3.2.254 dev enp0s3

sudo systemctl restart NetworkManager

```
  - Pour john :
```bash
sudo ip route add 10.3.2.0/24 via 10.3.1.254 dev enp0s3
```
Add route definitif :
```bash
[slayz@localhost ~]$ sudo nano /etc/sysconfig/network-scripts/route-enp0s3

10.3.2.0 via 10.3.1.254 dev enp0s3

sudo systemctl restart NetworkManager

```
Ping john :
```bash
ping 10.3.1.11
```
**Réponse :**
```bash
[slayz@localhost ~]$ ping 10.3.1.11
PING 10.3.1.11 (10.3.1.11) 56(84) bytes of data.
64 bytes from 10.3.1.11: icmp_seq=1 ttl=63 time=1.93 ms
64 bytes from 10.3.1.11: icmp_seq=2 ttl=63 time=1.83 ms
64 bytes from 10.3.1.11: icmp_seq=3 ttl=63 time=1.72 ms

--- 10.3.1.11 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 1.715/1.825/1.934/0.089 ms
```

## 2. Analyse de trames

| ordre | type trame  | IP source | MAC source                | IP destination | MAC destination            |
| ----- | ----------- | --------- | ------------------------- | -------------- | -------------------------- |
| 1     | Requête ARP | x         | `john` `08:00:27:b2:92:c0`| x              | Broadcast `FF:FF:FF:FF:FF` |
| 2     | Réponse ARP | x        |`routeur``08:00:27:7a:6f:39`| x             | `john`  `08:00:27:b2:92:c0`|
| ...   | ...         | ...       | ...                       |                |                            |
| ?     | Ping        | 10.3.1.11 |`john` `08:00:27:b2:92:c0` | 10.3.2.12      |`routeur``08:00:27:4d:a3:18`|
| ?     | Pong        | 10.3.2.12 |`routeur``08:00:27:4d:a3:18`| 10.3.1.11     | `john` `08:00:27:b2:92:c0` |
