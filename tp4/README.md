# TP4 : DHCP (CHORT Maxime)
- [I. DHCP Client](#i-dhcp-client)
- [II. Serveur DHCP](#ii-serveur-dhcp)
  - [1. Setup topologie](#1-setup-topologie)
  - [2. Serveur DHCP](#2-serveur-dhcp)
  - [3. Client DHCP](#3-client-dhcp)
  - [4. Options DHCP](#4-options-dhcp)
## I. DHCP Client 
**🌞 Déterminer**

- l'adresse du serveur DHCP :
```bash
ipconfig /all
```
**Réponse :**
```bash
PS C:\Users\melb3> ipconfig /all

Configuration IP de Windows
Carte réseau sans fil Wi-Fi :
   Serveur DHCP . . . . . . . . . . . . . : 10.33.79.254
```
- l'heure exacte à laquelle vous avez obtenu votre bail DHCP
```bash
PS C:\Users\melb3> ipconfig /all

Configuration IP de Windows
Carte réseau sans fil Wi-Fi :
   Bail obtenu. . . . . . . . . . . . . . : vendredi 27 octobre 2023 08:40:34
```
- l'heure exacte à laquelle il va expirer
```bash
PS C:\Users\melb3> ipconfig /all

Configuration IP de Windows
Carte réseau sans fil Wi-Fi :
   Bail expirant. . . . . . . . . . . . . : samedi 28 octobre 2023 08:40:32
```

**🌞 Capturer un échange DHCP**  

[Clique ici pour voir l'échange DORA ](./tp4_dhcp_client.pcapng) 

**🌞 Analyser la capture Wireshark**  

- parmi ces 4 trames, laquelle contient les informations proposées au client ?   

`DHCP Offer`  

## II. Serveur DHCP
### 1. Setup topologie
**🌞 Preuve de mise en place**
- depuis `dhcp.tp4.b1`, envoyer un `ping` vers un nom de domaine public (pas une IP)
```bash
[slayz@dhcp ~]$ ping google.com
PING google.com (172.217.20.206) 56(84) bytes of data.
64 bytes from waw02s08-in-f206.1e100.net (172.217.20.206): icmp_seq=1 ttl=115 time=17.7 ms
64 bytes from par10s50-in-f14.1e100.net (172.217.20.206): icmp_seq=2 ttl=115 time=16.9 ms
64 bytes from waw02s08-in-f206.1e100.net (172.217.20.206): icmp_seq=3 ttl=115 time=18.3 ms

--- google.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 16.897/17.622/18.288/0.569 ms
```
- depuis `node2.tp4.b1`, envoyer un `ping` vers un nom de domaine public (pas une IP)

```bash
[slayz@node2 ~]$ ping tukif.com
PING tukif.com (104.18.23.215) 56(84) bytes of data.
64 bytes from 104.18.23.215 (104.18.23.215): icmp_seq=1 ttl=55 time=11.1 ms
64 bytes from 104.18.23.215 (104.18.23.215): icmp_seq=2 ttl=55 time=12.9 ms
64 bytes from 104.18.23.215 (104.18.23.215): icmp_seq=3 ttl=55 time=14.0 ms
^C
--- tukif.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
rtt min/avg/max/mdev = 11.109/12.692/14.039/1.207 ms
```

- depuis `node2.tp4.b1`, un `traceroute` vers une IP publique pour montrer que vos paquets à destination d'internet passent bien par le `router.tp4.b1`

```bash
[slayz@node2 ~]$ traceroute 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  _gateway (10.4.1.254)  0.575 ms  0.573 ms  0.561 ms
 2  10.0.3.2 (10.0.3.2)  0.549 ms  0.499 ms  0.481 ms
 3  * * *
 4  * * *
 5  * * *
 6  * * *
 7  * * *
 8  *^C
 ```

 ### 2. Serveur DHCP
**🌞 Rendu**

 ```bash
[slayz@dhcp ~]$ sudo dnf -y install dhcp-server
[slayz@dhcp ~]$ sudo nano /etc/dhcp/dhcpd.conf

 # create new
# nom de domaine
option domain-name     "srv.world";
# nom de serveur dns ou ip a mettre
option domain-name-servers     dlp.srv.world;
# tmps expiration bail par defaut
default-lease-time 600;
# tmps expiration max
max-lease-time 7200;
# activer dhcp dans le lan
authoritative;
# specify network address and subnetmask
subnet 10.4.1.0 netmask 255.255.255.0 {
    # definit l'intervalle d'ip que le protocole va donner
    range dynamic-bootp 10.4.1.137 10.4.1.237;
    # adresse de diffusion
    option broadcast-address 10.4.1.255;
    # specify passerelle
    option routers 10.4.1.254;
}
[slayz@dhcp ~]$ sudo systemctl enable --now dhcpd
[slayz@dhcp ~]$ sudo firewall-cmd --add-service=dhcp
[slayz@dhcp ~]$ sudo firewall-cmd --runtime-to-permanent
[slayz@dhcp ~]$ ll /var/lib/dhcpd
total 4
-rw-r--r--. 1 dhcpd dhcpd    0 Apr 20  2023 dhcpd6.leases
-rw-r--r--. 1 dhcpd dhcpd 1074 Oct 27 15:13 dhcpd.leases
-rw-r--r--. 1 dhcpd dhcpd    0 Apr 20  2023 dhcpd.leases~
```
- **un systemctl status dhcpd qui affiche l'état du serveur (je dois voir qu'il est actif)** 

```bash
[slayz@dhcp ~]$ sudo systemctl status dhcpd
[sudo] password for slayz:
● dhcpd.service - DHCPv4 Server Daemon
     Loaded: loaded (/usr/lib/systemd/system/dhcpd.service; enabled; preset: disabled)
     Active: active (running) since Fri 2023-10-27 15:00:13 CEST; 9min ago
       Docs: man:dhcpd(8)
             man:dhcpd.conf(5)
   Main PID: 1686 (dhcpd)
     Status: "Dispatching packets..."
      Tasks: 1 (limit: 4673)
     Memory: 5.2M
        CPU: 8ms
     CGroup: /system.slice/dhcpd.service
             └─1686 /usr/sbin/dhcpd -f -cf /etc/dhcp/dhcpd.conf -user dhcpd -group dhcpd --no-pid

Oct 27 15:00:13 dhcp dhcpd[1686]: Server starting service.
Oct 27 15:00:13 dhcp systemd[1]: Started DHCPv4 Server Daemon.
Oct 27 15:03:06 dhcp dhcpd[1686]: DHCPDISCOVER from 08:00:27:6f:dd:c2 via enp0s3
Oct 27 15:03:09 dhcp dhcpd[1686]: DHCPOFFER on 10.4.1.137 to 08:00:27:6f:dd:c2 via enp0s3
Oct 27 15:03:09 dhcp dhcpd[1686]: DHCPDISCOVER from 08:00:27:6f:dd:c2 via enp0s3
Oct 27 15:03:09 dhcp dhcpd[1686]: DHCPOFFER on 10.4.1.137 to 08:00:27:6f:dd:c2 via enp0s3
Oct 27 15:03:09 dhcp dhcpd[1686]: DHCPREQUEST for 10.4.1.137 (10.4.1.253) from 08:00:27:6f:dd:c2 via enp0s3
Oct 27 15:03:09 dhcp dhcpd[1686]: DHCPACK on 10.4.1.137 to 08:00:27:6f:dd:c2 via enp0s3
Oct 27 15:08:08 dhcp dhcpd[1686]: DHCPREQUEST for 10.4.1.137 from 08:00:27:6f:dd:c2 via enp0s3
Oct 27 15:08:08 dhcp dhcpd[1686]: DHCPACK on 10.4.1.137 to 08:00:27:6f:dd:c2 via enp0s3
```
- **je veux aussi un cat /etc/dhcp/dhcpd.conf dans le compte-rendu, pour que je vois le fichier de configuration**

```bash
[slayz@dhcp ~]$ cat /etc/dhcp/dhcpd.conf

 # create new
# nom de domaine
option domain-name     "srv.world";
# nom de serveur dns ou ip a mettre
option domain-name-servers     dlp.srv.world;
# tmps expiration bail par defaut
default-lease-time 600;
# tmps expiration max
max-lease-time 7200;
# activer dhcp dans le lan
authoritative;
# specify network address and subnetmask
subnet 10.4.1.0 netmask 255.255.255.0 {
    # definit l'intervalle d'ip que le protocole va donner
    range dynamic-bootp 10.4.1.137 10.4.1.237;
    # adresse de diffusion
    option broadcast-address 10.4.1.255;
    # specify passerelle
    option routers 10.4.1.254;
}
```
### 3. Client DHCP
**🌞 Test !**
```bash
[slayz@node1 ~]$ sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s3

DEVICE=enp0s3

BOOTPROTO=dhcp
ONBOOT=yes
```
**🌞 Prouvez que**

```bash
[slayz@node1 ~]$ ip a
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:6f:dd:c2 brd ff:ff:ff:ff:ff:ff
    inet 10.4.1.137/24 brd 10.4.1.255 scope global ⭐ dynamic ⭐ noprefixroute enp0s3
       valid_lft 513sec preferred_lft 513sec
    inet6 fe80::a00:27ff:fe6f:ddc2/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```
- déterminer la date exacte de création du bail :
```bash
[slayz@node1 ~]$ sudo nmcli con show enp0s3
connection.timestamp:                   1698413384
#vendredi 27 octobre 2023 15:29:44
```
- déterminer la date exacte d'expiration  
```bash 
[slayz@node1 ~]$ sudo nmcli con show enp0s3
DHCP4.OPTION[7]:                        expiry = 1698415435
#vendredi 27 octobre 2023 16:03:55
```
- déterminer l'adresse IP du serveur DHCP
```bash
[slayz@node1 ~]$ sudo nmcli con show enp0s3
DHCP4.OPTION[4]:                        dhcp_server_identifier = 10.4.1.253
```
- vous pouvez ping router.tp4.b1 et node2.tp4.b1 grâce à cette nouvelle IP récupérée
  - routeur :

```bash
  [slayz@node1 ~]$ ping 10.4.1.254
PING 10.4.1.254 (10.4.1.254) 56(84) bytes of data.
64 bytes from 10.4.1.254: icmp_seq=1 ttl=64 time=0.578 ms
64 bytes from 10.4.1.254: icmp_seq=2 ttl=64 time=0.901 ms
^C
--- 10.4.1.254 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 0.578/0.739/0.901/0.161 ms
```

  - node2
```bash
[slayz@node1 ~]$ ping 10.4.1.12
PING 10.4.1.12 (10.4.1.12) 56(84) bytes of data.
64 bytes from 10.4.1.12: icmp_seq=1 ttl=64 time=1.21 ms
64 bytes from 10.4.1.12: icmp_seq=2 ttl=64 time=0.882 ms
64 bytes from 10.4.1.12: icmp_seq=3 ttl=64 time=0.860 ms
^C
--- 10.4.1.12 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
rtt min/avg/max/mdev = 0.860/0.984/1.210/0.160 ms
```

**🌞 Bail DHCP serveur**
```bash
[slayz@node1 ~]$ cat /var/lib/dhcpd/dhcpd.leases


# The format of this file is documented in the dhcpd.leases(5) manual page.
# This lease file was written by isc-dhcp-4.4.2b1

# authoring-byte-order entry is generated, DO NOT DELETE
authoring-byte-order little-endian;

lease 10.4.1.137 {
  starts 5 2023/10/27 15:06:34;
  ends 5 2023/10/27 15:16:34;
  cltt 5 2023/10/27 15:06:34;
  binding state active;
  next binding state free;
  rewind binding state free;
  hardware ethernet 08:00:27:6f:dd:c2;
  uid "\001\010\000'o\335\302";
}
```

## 4. Options DHCP
**🌞 Nouvelle conf !**

```bash
[slayz@dhcp etc]$ sudo cat dhcp/dhcpd.conf
# create new
# nom de domaine
option domain-name     "srv.world";
# nom de serveur dns ou ip a mettre
option domain-name-servers      1.1.1.1;
# tmps expiration bail par defaut
default-lease-time 21600;
# tmps expiration max
max-lease-time 22000;
# activer dhcp dans le lan
authoritative;
# specify network address and subnetmask
subnet 10.4.1.0 netmask 255.255.255.0 {
    # definit l'intervalle d'ip que le protocole va donner
    range dynamic-bootp 10.4.1.137 10.4.1.237;
    # adresse de diffusion
    option broadcast-address 10.4.1.255;
    # specify passerelle
    option routers 10.4.1.254;
}
```
**🌞 Test !**  
- redemandez une IP avec le client `node1.tp4.b1`
```bash
sudo dhclient -r enp0s3
sudo dhclient enp0s3
```
- prouvez-que :
  - vous avez enregistré l'adresse d'un serveur DNS
```bash
[slayz@dhcp etc]$ cat resolv.conf
# Generated by NetworkManager
nameserver 1.1.1.1
```
- vous avez une nouvelle route par défaut qui a été récupérée dynamiquement  
```bash
[slayz@node1 ~]$ ip r s
default via 10.4.1.254 dev enp0s3
⭐ default via 10.4.1.254 dev enp0s3 proto dhcp src 10.4.1.137 metric 100 ⭐
10.4.1.0/24 dev enp0s3 proto kernel scope link src 10.4.1.137 metric 100
```
  - la durée de votre bail DHCP est bien de 6 heures
```bash
[slayz@node1 dhclient]$ cd var/lib/dhclient

[slayz@node1 dhclient]$ cat dhclient.leases
lease {
  interface "enp0s3";
  fixed-address 10.4.1.138;
  option subnet-mask 255.255.255.0;
  option routers 10.4.1.254;
  option dhcp-lease-time 21600;
  option dhcp-message-type 5;
  option domain-name-servers 1.1.1.1;
  option dhcp-server-identifier 10.4.1.253;
  option broadcast-address 10.4.1.255;
  option domain-name "srv.world";
  renew 5 2023/10/27 15:47:04;
  rebind 5 2023/10/27 15:47:04;
  expire 5 2023/10/27 15:47:04;
```
- prouvez que vous avez un accès Internet après cet échange DHCP
```bash
[slayz@node1 ~]$ ping xhamster.com
PING xhamster.com (104.18.184.10) 56(84) bytes of data.
64 bytes from 104.18.184.10 (104.18.184.10): icmp_seq=1 ttl=54 time=32.8 ms
64 bytes from 104.18.184.10 (104.18.184.10): icmp_seq=2 ttl=54 time=31.3 ms
64 bytes from 104.18.184.10 (104.18.184.10): icmp_seq=3 ttl=54 time=32.3 ms
^C
--- xhamster.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 31.345/32.143/32.766/0.593 ms
```
**🌞 Capture Wireshark**  

- utilisez tcpdump pour capturer un échange DHCP complet entre node1.tp4.b1 et dhcp.tp4.b1   

[Clique ici pour voir l'échange DORA ](./tp4_dhcp_serveur.pcapng) 