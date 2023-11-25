# TP6 : Un LAN maîtrisé meo ?
- [II. Serveur DNS](#ii-serveur-dns)
    - [1. Setup](#1-setup)
    - [3. Test](#3-test)
- [III. Serveur DHCP](#iii-serveur-dhcp)
## II. Serveur DNS
### 1. Setup
**🌞 Dans le rendu, je veux**
- un cat des 3 fichiers de conf (fichier principal + deux fichiers de zone) :
```bash
[slayz@dns ~]$ sudo cat /etc/named.conf
options {
        listen-on port 53 { 127.0.0.1; any; };
        listen-on-v6 port 53 { ::1; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        secroots-file   "/var/named/data/named.secroots";
        recursing-file  "/var/named/data/named.recursing";
        allow-query     { localhost; any; };
        allow-query-cache { localhost; any; };

        recursion yes;

        dnssec-validation yes;

        managed-keys-directory "/var/named/dynamic";
        geoip-directory "/usr/share/GeoIP";

        pid-file "/run/named/named.pid";
        session-keyfile "/run/named/session.key";

        /* https://fedoraproject.org/wiki/Changes/CryptoPolicy */
        include "/etc/crypto-policies/back-ends/bind.config";
};

logging {
        channel default_debug {
                file "data/named.run";
                severity dynamic;
        };
};

zone "." IN {
        type hint;
        file "named.ca";
};

include "/etc/named.rfc1912.zones";
include "/etc/named.root.key";

# référence vers notre fichier de zone
zone "tp6.b1" IN {
     type master;
     file "tp6.b1.db";
     allow-update { none; };
     allow-query {any; };
};
# référence vers notre fichier de zone inverse
zone "1.4.10.in-addr.arpa" IN {
     type master;
     file "tp6.b1.rev";
     allow-update { none; };
     allow-query { any; };
};
```
```bash
[slayz@dns ~]$ sudo cat /var/named/tp6.b1.db
$TTL 86400
@ IN SOA dns.tp6.b1. admin.tp6.b1. (
    2019061800 ;Serial
    3600 ;Refresh
    1800 ;Retry
    604800 ;Expire
    86400 ;Minimum TTL
)

@ IN NS dns.tp6.b1.

dns       IN A 10.6.1.101
john      IN A 10.6.1.11
```

```bash
[slayz@dns ~]$ sudo cat /var/named/tp6.b1.rev
$TTL 86400
@ IN SOA dns.tp6.b1. admin.tp6.b1. (
    2019061800 ;Serial
    3600 ;Refresh
    1800 ;Retry
    604800 ;Expire
    86400 ;Minimum TTL
)

@ IN NS dns.tp6.b1.

101 IN PTR dns.tp6.b1.
11 IN PTR john.tp6.b1.
```

- un systemctl status named qui prouve que le service tourne bien :

```bash
[slayz@dns ~]$ sudo systemctl status named
● named.service - Berkeley Internet Name Domain (DNS)
     Loaded: loaded (/usr/lib/systemd/system/named.service; enabled; preset: disabled)
     Active: active (running) since Fri 2023-11-17 16:12:17 CET; 9min ago
   Main PID: 2101 (named)
      Tasks: 5 (limit: 4673)
     Memory: 19.2M
        CPU: 135ms
     CGroup: /system.slice/named.service
             └─2101 /usr/sbin/named -u named -c /etc/named.conf

Nov 17 16:12:17 dns.tp6.b1 named[2101]: network unreachable resolving './DNSKEY/IN': 2001:7fe::53#53
Nov 17 16:12:17 dns.tp6.b1 named[2101]: network unreachable resolving './NS/IN': 2001:7fe::53#53
Nov 17 16:12:17 dns.tp6.b1 named[2101]: zone 1.0.0.127.in-addr.arpa/IN: loaded serial 0
Nov 17 16:12:17 dns.tp6.b1 named[2101]: zone localhost/IN: loaded serial 0
Nov 17 16:12:17 dns.tp6.b1 named[2101]: zone localhost.localdomain/IN: loaded serial 0
Nov 17 16:12:17 dns.tp6.b1 named[2101]: all zones loaded
Nov 17 16:12:17 dns.tp6.b1 systemd[1]: Started Berkeley Internet Name Domain (DNS).
Nov 17 16:12:17 dns.tp6.b1 named[2101]: running
Nov 17 16:12:17 dns.tp6.b1 named[2101]: managed-keys-zone: Initializing automatic trust anchor management for zone '.'; DNS>
Nov 17 16:12:17 dns.tp6.b1 named[2101]: resolver priming query complete
```
- une commande ss qui prouve que le service écoute bien sur un port :

```bash
[slayz@dns ~]$ sudo ss -l -t -n -a -p -u | grep "10.6.1.101"
udp   UNCONN 0      0         10.6.1.101:53        0.0.0.0:*     users:(("named",pid=2101,fd=19))
tcp   LISTEN 0      10        10.6.1.101:53        0.0.0.0:*     users:(("named",pid=2101,fd=21))

```
**🌞 Ouvrez le bon port dans le firewall**
```bash
[slayz@dns ~]$ sudo firewall-cmd --add-port=53/udp --permanent
success
[slayz@dns ~]$ sudo firewall-cmd --reload
success
```
### 3. Test
**🌞 Sur la machine `john.tp6.b1`**
- configurez la machine pour qu'elle utilise votre serveur DNS quand elle a besoin de résoudre des noms :
```bash
[slayz@john ~]$ cat /etc/resolv.conf
# Generated by NetworkManager
nameserver 10.6.1.101
```
- assurez-vous que vous pouvez :
    - résoudre des noms comme `john.tp6.b1` et `dns.tp6.b1`
```bash
[slayz@john ~]$ ping john.tp6.b1
PING john.tp6.b1 (10.6.1.11) 56(84) bytes of data.
64 bytes from john.tp6.b1 (10.6.1.11): icmp_seq=1 ttl=64 time=0.150 ms
64 bytes from john.tp6.b1 (10.6.1.11): icmp_seq=2 ttl=64 time=0.114 ms
^C
--- john.tp6.b1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 0.114/0.132/0.150/0.018 ms
[slayz@john ~]$ ping dns.tp6.b1
PING dns.tp6.b1 (10.6.1.101) 56(84) bytes of data.
64 bytes from 10.6.1.101 (10.6.1.101): icmp_seq=1 ttl=64 time=1.73 ms
64 bytes from 10.6.1.101 (10.6.1.101): icmp_seq=2 ttl=64 time=2.03 ms
^C
--- dns.tp6.b1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1003ms
rtt min/avg/max/mdev = 1.726/1.878/2.031/0.152 ms
```
- mais aussi des noms comme `www.ynov.com` :
```bash
[slayz@john ~]$ ping www.ynov.com
PING www.ynov.com (172.67.74.226) 56(84) bytes of data.
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=1 ttl=56 time=14.2 ms
64 bytes from 172.67.74.226 (172.67.74.226): icmp_seq=2 ttl=56 time=14.5 ms
^C
--- www.ynov.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1198ms
rtt min/avg/max/mdev = 14.238/14.360/14.482/0.122 ms
```
**🌞 Sur votre PC**
- utilisez une commande pour résoudre le nom john.tp6.b1 en utilisant 10.6.1.101 comme serveur DNS :
```bash
PS C:\Windows\system32> Set-DNSClientServerAddress "Wi-Fi" -ServerAddresses ("10.6.1.101")
PS C:\Users\melb3> Get-DnsClientServerAddress

InterfaceAlias               Interface Address ServerAddresses
                             Index     Family
--------------               --------- ------- ---------------
Wi-Fi                               16 IPv4    {10.6.1.101}

PS C:\Users\melb3> nslookup john.tp6.b1
Serveur :   UnKnown
Address:  10.6.1.101

Nom :    john.tp6.b1
Address:  10.6.1.11
```
[Clique ici pour voir une requête DNS vers le nom `john.tp6.b1` ainsi que la réponse ](./wireshark/tp6_dns.pcapng)
## III. Serveur DHCP
**🌞 Installer un serveur DHCP**
```bash
[slayz@dhcp ~]$ sudo cat /etc/dhcp/dhcpd.conf
[sudo] password for slayz:
#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp-server/dhcpd.conf.example
#   see dhcpd.conf(5) man page
#
# create new
# specify domain name
option domain-name     "dns.tp6.b1";
# specify DNS server's hostname or IP address
option domain-name-servers     10.6.1.101;
# default lease time
default-lease-time 600;
# max lease time
max-lease-time 7200;
# this DHCP server to be declared valid
authoritative;
# specify network address and subnetmask
subnet 10.6.1.0 netmask 255.255.255.0 {
    # specify the range of lease IP address
    range dynamic-bootp 10.6.1.13 10.6.1.37;
    # specify broadcast address
    option broadcast-address 10.6.1.255;
    # specify gateway
    option routers 10.6.1.254;
}
```
**🌞 Test avec john.tp6.b1**
- prouvez-que :
    - vous avez une IP dans la bonne range
```bash
[slayz@john ~]$ ip a
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:a3:16:88 brd ff:ff:ff:ff:ff:ff
    inet ⭐10.6.1.13/24⭐ brd 10.6.1.255 scope global dynamic noprefixroute enp0s3
       valid_lft 570sec preferred_lft 570sec
    inet6 fe80::a00:27ff:fea3:1688/64 scope link
       valid_lft forever preferred_lft forever
```

- vous avez votre routeur configuré automatiquement comme passerelle
```bash
[slayz@john ~]$ ip r s
default via 10.6.1.254 dev enp0s3 proto dhcp src 10.6.1.13 metric 100
```
- vous avez votre serveur DNS automatiquement configuré 
```bash
[slayz@john ~]$ cat /etc/resolv.conf
# Generated by NetworkManager
search srv.world
nameserver 10.6.1.101
```
- vous avez un accès internet
```bash
[slayz@john ~]$ ping google.com
PING google.com (142.250.201.174) 56(84) bytes of data.
64 bytes from par21s23-in-f14.1e100.net (142.250.201.174): icmp_seq=1 ttl=115 time=15.9 ms
64 bytes from par21s23-in-f14.1e100.net (142.250.201.174): icmp_seq=2 ttl=115 time=18.7 ms
^C
--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1003ms
rtt min/avg/max/mdev = 15.918/17.317/18.717/1.399 ms
```
- Requête web avec `john.tp6.b1`
```bash
[slayz@john ~]$ curl www.ynov.com
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>302 Found</title>
</head><body>
<h1>Found</h1>
<p>The document has moved <a href="https://www.ynov.com/">here</a>.</p>
<hr>
<address>Apache/2.4.41 (Ubuntu) Server at ynov.com Port 80</address>
</body></html>
```
[Clique ici pour voir requête DNS qui est effectuée automatiquement, la réponse DNS, le 3-way handshake TCP vers le serveur web, l'échange HTTP/HTTPS ](./wireshark/tp6_web.pcapng)
