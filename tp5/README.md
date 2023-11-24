# TP5: TCP, UDP et services réseau
- [I. First steps](#i-first-steps)
- [II. Setup Virtuel](#ii-setup-virtuel)
  - [1. SSH](#1-ssh)
  - [2. Routage](#2-routage)
  - [3. Serveur Web](#3-serveur-web)

## I. First steps

**🌞 Déterminez, pour ces 5 applications, si c'est du TCP ou de l'UDP** 

- Discord : UDP
    - IP et port du serveur auquel vous vous connectez : 66.22.198.164:50004
    - le port local que vous ouvrez pour vous connecter : 56260

- Téléchargement STEAM : TCP
    - IP et port du serveur auquel vous vous connectez :185.25.182.5:443 
    - le port local que vous ouvrez pour vous connecter : 55830 

- Pornhub : TCP
    - IP et port du serveur auquel vous vous connectez : 152.195.34.118:443
    - le port local que vous ouvrez pour vous connecter : 57524

- vidéo Youtube : UDP 
    - IP et port du serveur auquel vous vous connectez : 64.15.115.20:443
    - le port local que vous ouvrez pour vous connecter : 51962

- Spotify : UDP 
    - IP et port du serveur auquel vous vous connectez : 35.186.224.18:443
    - le port local que vous ouvrez pour vous connecter : 53443

**🌞 Demandez l'avis à votre OS**

- Discord :
```bash
PS C:\WINDOWS\system32> netstat -n -a -b | Select-String Disc -Context 1,0

    TCP    127.0.0.1:6463         0.0.0.0:0              LISTENING
>  [Discord.exe]
    TCP    172.20.10.5:58371      162.159.136.234:443    ESTABLISHED
>  [Discord.exe]
    TCP    172.20.10.5:58381      34.111.115.192:443     ESTABLISHED
>  [Discord.exe]
    TCP    172.20.10.5:58902      162.159.61.3:443       ESTABLISHED
>  [Discord.exe]
    TCP    172.20.10.5:58903      162.159.61.3:443       ESTABLISHED
>  [Discord.exe]
```
[Clique ici pour voir le trafic DISCORD ](./wireshark/tp5_service_1.pcapng)

- Téléchargement STEAM : 
```bash
PS C:\WINDOWS\system32> netstat -n -a -b | Select-String Steam.exe -Context 1,0

>  [steam.exe]
    TCP    172.20.10.5:60920      185.25.182.40:443      ESTABLISHED
>  [steam.exe]
    TCP    172.20.10.5:60921      185.25.182.36:443      ESTABLISHED
>  [steam.exe]
    TCP    172.20.10.5:60922      185.25.182.37:443      ESTABLISHED
>  [steam.exe]
    TCP    172.20.10.5:60946      185.25.182.3:443       ESTABLISHED
>  [steam.exe]
    TCP    172.20.10.5:60951      185.25.182.35:443      ESTABLISHED
>  [steam.exe]
    TCP    172.20.10.5:61036      185.25.182.3:443       ESTABLISHED
>  [steam.exe]
    TCP    172.20.10.5:61037      185.25.182.3:443       ESTABLISHED
>  [steam.exe]
    TCP    172.20.10.5:61038      185.25.182.3:443       ESTABLISHED
>  [steam.exe]
    TCP    172.20.10.5:61039      185.25.182.5:443       ESTABLISHED
>  [steam.exe]
    TCP    172.20.10.5:61040      185.25.182.3:443       ESTABLISHED
>  [steam.exe]
    TCP    172.20.10.5:61041      185.25.182.3:443       ESTABLISHED
>  [steam.exe]
    TCP    172.20.10.5:61043      185.25.182.3:443       ESTABLISHED
```
[Clique ici pour voir le trafic steam ](./wireshark/tp5_service_2.pcapng)

- Pornhub : 
```bash
PS C:\WINDOWS\system32> netstat -n -a -b | Select-String chrome.exe -Context 1,0

>  [chrome.exe]
    TCP    172.20.10.5:61136      216.58.215.42:443      ESTABLISHED
>  [chrome.exe]
    TCP    172.20.10.5:61137      104.16.124.175:443     ESTABLISHED
>  [chrome.exe]
    TCP    172.20.10.5:61138      216.239.32.36:443      ESTABLISHED
>  [chrome.exe]
    TCP    172.20.10.5:61142      66.254.114.62:443      ESTABLISHED
>  [chrome.exe]
    TCP    172.20.10.5:61143      142.250.75.238:443     ESTABLISHED
>  [chrome.exe]
    TCP    172.20.10.5:61144      216.58.213.67:443      ESTABLISHED
>  [chrome.exe]
    TCP    172.20.10.5:61147      152.195.34.118:443     ESTABLISHED
```
[Clique ici pour voir le trafic d'une vidéo pornhub ](./wireshark/tp5_service_3.pcapng)

- vidéo Youtube :
```bash
PS C:\WINDOWS\system32> netstat -n -a -b | Select-String chrome.exe -Context 1,0

>  [chrome.exe]
    TCP    172.20.10.5:61370      142.250.179.86:443     ESTABLISHED
>  [chrome.exe]
    TCP    172.20.10.5:61371      142.250.179.86:443     ESTABLISHED
>  [chrome.exe]
    TCP    172.20.10.5:61372      142.250.179.98:443     ESTABLISHED
>  [chrome.exe]
    TCP    172.20.10.5:61374      142.250.178.134:443    ESTABLISHED
>  [chrome.exe]
    TCP    172.20.10.5:61375      142.250.179.78:443     ESTABLISHED
>  [chrome.exe]
    TCP    172.20.10.5:61376      142.250.178.138:443    ESTABLISHED
>  [chrome.exe]
    TCP    172.20.10.5:61377      142.250.201.166:443    ESTABLISHED

    TCP    172.20.10.5:61397      142.250.178.142:443    ESTABLISHED
```

[Clique ici pour voir le trafic d'une vidéo Ytb ](./wireshark/tp5_service_4.pcapng)

- Spotify : 
```bash
PS C:\WINDOWS\system32> netstat -n -a -b | Select-String spotify -Context 1,0

>  [Spotify.exe]
    TCP    172.20.10.5:57874      34.149.154.214:443     ESTABLISHED
>  [Spotify.exe]
    TCP    172.20.10.5:57877      34.149.154.214:443     ESTABLISHED
>  [Spotify.exe]
    TCP    172.20.10.5:57971      35.186.224.18:443      ESTABLISHED
>  [Spotify.exe]
    TCP    172.20.10.5:61328      146.75.118.248:443     ESTABLISHED
>  [Spotify.exe]
    TCP    172.20.10.5:61351      35.186.224.25:443      ESTABLISHED
>  [Spotify.exe]
    TCP    172.20.10.5:61491      146.75.74.248:443      ESTABLISHED
>  [Spotify.exe]
    TCP    172.20.10.5:61492      146.75.74.248:443      ESTABLISHED
>  [Spotify.exe]
    TCP    172.20.10.5:61493      146.75.74.248:443      ESTABLISHED
>  [Spotify.exe]
    TCP    172.20.10.5:61494      146.75.74.248:443      ESTABLISHED
>  [Spotify.exe]
    TCP    172.20.10.5:61495      146.75.74.248:443      ESTABLISHED
>  [Spotify.exe]
    TCP    172.20.10.5:61496      146.75.74.248:443      ESTABLISHED
>  [Spotify.exe]
    TCP    172.20.10.5:61501      199.232.82.248:443     ESTABLISHED
>  [Spotify.exe]
    TCP    172.20.10.5:61502      23.72.250.50:443       ESTABLISHED
>  [Spotify.exe]
    TCP    172.20.10.5:61505      35.186.224.25:443      ESTABLISHED
```
[Clique ici pour voir le trafic d'une musique spotify ](./wireshark/tp5_service_5.pcapng)

## II. Setup Virtuel
### 1. SSH

**🌞 Examinez le trafic dans Wireshark**
- déterminez si SSH utilise TCP ou UDP :  
SSH utilise TCP car il faut une connexion safe
- repérez le 3-Way Handshake à l'établissement de la connexion : 
- repérez du trafic SSH :
- repérez le FIN ACK à la fin d'une connexion : 

**🌞 Demandez aux OS**
- repérez, avec une commande adaptée (netstat ou ss), la connexion SSH depuis votre machine : 

```bash
PS C:\WINDOWS\system32> netstat -n -a -b | Select-String 10.5.1.1 -Context 1,0

   Impossible d'obtenir les informations de propriétaire
>   TCP    10.5.1.1:139           0.0.0.0:0              LISTENING
   Impossible d'obtenir les informations de propriétaire
>   TCP    10.5.1.1:53641         10.5.1.11:22           ESTABLISHED
```
- ET repérez la connexion SSH depuis la VM :

```bash
[slayz@node1 ~]$ ss -t
State       Recv-Q       Send-Q              Local Address:Port               Peer Address:Port        Process
ESTAB       0            0                       10.5.1.11:ssh                    10.5.1.1:53641
```
ou :

```bash
[slayz@node1 ~]$ ss -n -a -b -t
State      Recv-Q     Send-Q         Local Address:Port         Peer Address:Port     Process
ESTAB      0          0                  10.5.1.11:22               10.5.1.1:53641     timer:(keepalive,97min,0)
```

[Clique ici pour voir la capture avec le 3-way handshake, un peu de trafic au milieu et une fin de connexion](../wireshark/tp5_3_way2.pcapng)

## 2. Routage

**🌞 Prouvez que**

- `node1.tp5.b1` a un accès internet :

```bash
[slayz@node1 ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=117 time=34.4 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=117 time=32.5 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=117 time=31.8 ms
^C
--- 8.8.8.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2004ms
rtt min/avg/max/mdev = 31.759/32.899/34.405/1.110 ms
```

- `node1.tp5.b1` peut résoudre des noms de domaine publics :
```bash
[slayz@node1 ~]$ ping google.com
PING google.com (216.58.214.174) 56(84) bytes of data.
64 bytes from par10s42-in-f14.1e100.net (216.58.214.174): icmp_seq=1 ttl=118 time=31.1 ms
64 bytes from par10s42-in-f14.1e100.net (216.58.214.174): icmp_seq=2 ttl=118 time=32.5 ms
^C
--- google.com ping statistics ---
3 packets transmitted, 2 received, 33.3333% packet loss, time 2004ms
rtt min/avg/max/mdev = 31.090/31.804/32.518/0.714 ms
```

## 3. Serveur Web

**🌞 Installez le paquet nginx**
```bash
[slayz@web ~]$ sudo dnf install nginx -y
```
**🌞 Créer le site web**
- créer donc un dossier /var/www/site_web_nul/
- créer un fichier /var/www/site_web_nul/index.html qui contient un code HTML simpliste de votre choix
    - un p'tit `<h1>MEOW</h1>` ça suffit hein
```bash
[slayz@web var]$ sudo mkdir www
[slayz@web var]$ cd www/
[slayz@web www]$ sudo mkdir site_web_nul
[slayz@web www]$ cd site_web_nul/
[slayz@web site_web_nul]$ sudo touch index.html
[slayz@web site_web_nul]$ sudo nano index.html
<h1>MEOW</h1>
```
**🌞 Donner les bonnes permissions**
```bash
[slayz@web site_web_nul]$ sudo chown -R nginx:nginx /var/www/site_web_nul
[slayz@web www]$ ls -l
total 0
drwxr-xr-x. 2 nginx nginx 24 Nov 10 18:33 site_web_nul
```
**🌞 Créer un fichier de configuration NGINX pour notre site web**
```bash
[slayz@web ~]$ cd /etc/nginx/conf.d/
[slayz@web conf.d]$ sudo touch site_web_nul.conf
[sudo] password for slayz:
[slayz@web conf.d]$ sudo nano site_web_nul.conf

server {
  # le port sur lequel on veut écouter
  listen 80;

  # le nom de la page d'accueil si le client de la précise pas
  index index.html;

  # un nom pour notre serveur
  server_name www.site_web_nul.b1;

  # le dossier qui contient notre site web
  root /var/www/site_web_nul;
}
```
**🌞 Démarrer le serveur web !**
```bash
[slayz@web ~]$ sudo systemctl start nginx
[sudo] password for slayz:

[slayz@web ~]$ systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; preset: disabled)
     Active: active (running) since Fri 2023-11-10 18:51:42 CET; 15s ago
    Process: 1790 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
    Process: 1791 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
    Process: 1792 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
   Main PID: 1793 (nginx)
      Tasks: 2 (limit: 4673)
     Memory: 2.0M
        CPU: 16ms
     CGroup: /system.slice/nginx.service
             ├─1793 "nginx: master process /usr/sbin/nginx"
             └─1794 "nginx: worker process"

Nov 10 18:51:42 web.tp5.b1 systemd[1]: Starting The nginx HTTP and reverse proxy server...
Nov 10 18:51:42 web.tp5.b1 nginx[1791]: nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
Nov 10 18:51:42 web.tp5.b1 nginx[1791]: nginx: configuration file /etc/nginx/nginx.conf test is successful
Nov 10 18:51:42 web.tp5.b1 systemd[1]: Started The nginx HTTP and reverse proxy server.
```
**🌞 Ouvrir le port firewall**
```bash
[slayz@web ~]$ sudo firewall-cmd --add-port=80/tcp --permanent
success
[slayz@web ~]$ sudo firewall-cmd --reload
success
```
**🌞 Visitez le serveur web !**
```bash
[slayz@node1 ~]$ curl 10.5.1.12:80
<h1>MEOW</h1>
```
**🌞 Visualiser le port en écoute**
```bash
[slayz@web ~]$ sudo ss -l -t -n
State                   Recv-Q                  Send-Q                                   Local Address:Port                                   Peer Address:Port                 Process
LISTEN                  0                       511                                            0.0.0.0:80                                          0.0.0.0:*
LISTEN                  0                       511                                               [::]:80                                             [::]:*
```
**🌞 Analyse trafic**

- repérez :
    - le 3 way handshake TCP :
    - du trafic HTTP : 
    - le contenu de la page HTML retourné :

[Clique ici pour voir le 3-way handshake, la page HTML retournée, et une fin de connexion ](./wireshark/tp5_web.pcapng)

```bash
[slayz@dns ~]$ sudo ss -l -t -n | grep "10.6.1.101"
LISTEN 0      10        10.6.1.101:53        0.0.0.0:*
```
```bash
[slayz@dns ~]$ sudo firewall-cmd --add-port=53/tcp --permanent
success
[slayz@dns ~]$ sudo firewall-cmd --reload
success
```
### 3. Test
