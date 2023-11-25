# TP7 : Do u secure
- [II. SSH](#ii-ssh)
    - [1. Fingerprint](#1-fingerprint)
        - [B. Manip](#b-manip)
    - [2. Conf serveur SSH](#2-conf-serveur-ssh)
    - [3. Connexion par clé](#3-connexion-par-clé) 
        - [B. Manips](#b-manips)
        - [C. Changement de fingerprint](#c-changement-de-fingerprint)
- [III. Web sécurisé](#iii-web-sécurisé)
    - [0. Setup](#0-setup)
    - [1. Setup HTTPS](#1-setup-https)
- [IV. VPN](#iv-vpn)
    - [3. On y va ou bien](#3-on-y-va-ou-bien)
        - [A. Setup serveur](#a-setup-serveur)
        - [B. Setup client](#b-setup-client)



# II. SSH
## 1. Fingerprint
### B. Manip

**🌞 Effectuez une connexion SSH en vérifiant le fingerprint**

- en rendu je veux voir le message du serveur à la première connexion :
    - une commande ssh pour se connecter vers john

```bash
PS C:\Users\melb3> ssh slayz@10.7.1.11
The authenticity of host '10.7.1.11 (10.7.1.11)' can't be established.
ED25519 key fingerprint is SHA256:f49nzRoDxA/WtCf14jVptxcuRrgZrUCAcVTmbZ3VMz4.
This host key is known by the following other names/addresses:
    C:\Users\melb3/.ssh/known_hosts:16: 10.3.1.12
    C:\Users\melb3/.ssh/known_hosts:19: 10.3.1.11
    C:\Users\melb3/.ssh/known_hosts:30: 10.6.1.101
    C:\Users\melb3/.ssh/known_hosts:31: 10.6.1.13
    C:\Users\melb3/.ssh/known_hosts:32: 10.6.1.102
    C:\Users\melb3/.ssh/known_hosts:33: 10.6.1.254
    C:\Users\melb3/.ssh/known_hosts:34: 10.6.1.103
    C:\Users\melb3/.ssh/known_hosts:35: 10.7.1.254
Are you sure you want to continue connecting (yes/no/[fingerprint])?    
```
- et je veux aussi une commande qui me montre l'empreinte du serveur :

```bash
[slayz@john ~]$ sudo ssh-keygen -l -f /etc/ssh/ssh_host_ed25519_key
[sudo] password for slayz:
256 SHA256:f49nzRoDxA/WtCf14jVptxcuRrgZrUCAcVTmbZ3VMz4 /etc/ssh/ssh_host_ed25519_key.pub (ED25519)
```
## 2. Conf serveur SSH
**🌞 Consulter l'état actuel**

```bash
[slayz@router ~]$ sudo ss -alntpu
Netid  State   Recv-Q  Send-Q   Local Address:Port   Peer Address:Port  Process
tcp    LISTEN  0       128            0.0.0.0:22          0.0.0.0:*      users:(("sshd",pid=682,fd=3))
```

**🌞 Modifier la configuration du serveur SSH**
```bash
[slayz@router ~]$ sudo cat /etc/ssh/sshd_config

Include /etc/ssh/sshd_config.d/*.conf
Port 22222
#AddressFamily any
ListenAddress 10.7.1.254
#ListenAddress ::
AuthorizedKeysFile      .ssh/authorized_keys
Subsystem       sftp    /usr/libexec/openssh/sftp-server
```

**🌞 Prouvez que le changement a pris effet**
```bash
[slayz@router ~]$ sudo ss -alntpu
Netid  State   Recv-Q  Send-Q   Local Address:Port    Peer Address:Port Process
tcp    LISTEN  0       128         10.7.1.254:22222        0.0.0.0:*     users:(("sshd",pid=2052,fd=3))
```
**🌞 N'oubliez pas d'ouvrir ce nouveau port dans le firewall**

```bash
[slayz@router ~]$ sudo firewall-cmd --add-port=22222/tcp --permanent
success
[slayz@router ~]$ sudo firewall-cmd --reload
success
```

**🌞 Effectuer une connexion SSH sur le nouveau port**
```bash
PS C:\Users\melb3> ssh slayz@router.tp7.b1 -p 22222
The authenticity of host '[router.tp7.b1]:22222 ([10.7.1.254]:22222)' can't be established.
ED25519 key fingerprint is SHA256:f49nzRoDxA/WtCf14jVptxcuRrgZrUCAcVTmbZ3VMz4.
This host key is known by the following other names/addresses:
    C:\Users\melb3/.ssh/known_hosts:16: 10.3.1.12
    C:\Users\melb3/.ssh/known_hosts:19: 10.3.1.11
    C:\Users\melb3/.ssh/known_hosts:30: 10.6.1.101
    C:\Users\melb3/.ssh/known_hosts:31: 10.6.1.13
    C:\Users\melb3/.ssh/known_hosts:32: 10.6.1.102
    C:\Users\melb3/.ssh/known_hosts:33: 10.6.1.254
    C:\Users\melb3/.ssh/known_hosts:34: 10.6.1.103
    C:\Users\melb3/.ssh/known_hosts:35: 10.7.1.254
    (1 additional names omitted)
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[router.tp7.b1]:22222' (ED25519) to the list of known hosts.
slayz@router.tp7.b1's password:
Last login: Thu Nov 23 14:27:29 2023 from 10.7.1.1
[slayz@router ~]$
```
## 3. Connexion par clé 
### B. Manips
**🌞 Générer une paire de clés**
```bash
PS C:\Users\melb3> ssh-keygen -t rsa -b 4096
Generating public/private rsa key pair.
Enter file in which to save the key (C:\Users\melb3/.ssh/id_rsa):
C:\Users\melb3/.ssh/id_rsa already exists.
Overwrite (y/n)? y
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in C:\Users\melb3/.ssh/id_rsa
Your public key has been saved in C:\Users\melb3/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:N9QaZLSxQ+KCUqH1bZiwMEzy1nOaS7NwIYxpbp3M/IY melb3@Maxime
The key's randomart image is:
+---[RSA 4096]----+
|.o+ =.  ..*      |
| *.B = = = =     |
|o.B * * + * .    |
|o.=o.* o . +     |
| o.**   S +      |
|.  +o+   . .     |
|   Eoo           |
|    .            |
|                 |
+----[SHA256]-----+
```

**🌞 Déposer la clé publique sur une VM**

```bash
melb3@Maxime MINGW64 ~
$ ssh-copy-id slayz@10.7.1.11
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/c/Users/melb3/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
slayz@10.7.1.11's password:

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'slayz@10.7.1.11'"
and check to make sure that only the key(s) you wanted were added.

```

**🌞 Connectez-vous en SSH à la machine**

```bash
PS C:\Users\melb3> ssh slayz@10.7.1.11
Last login: Thu Nov 23 15:13:45 2023 from 10.7.1.1
[slayz@john ~]$
```

## C. Changement de fingerprint
**🌞 Supprimer les clés sur la machine router.tp7.b1**

```bash
[slayz@router ssh]$ sudo rm /etc/ssh/ssh_host_*
[sudo] password for slayz:
[slayz@router ssh]$ ls
moduli  ssh_config  ssh_config.d  sshd_config  sshd_config.d
```
**🌞 Regénérez les clés sur la machine router.tp7.b1**
```bash
[slayz@router ssh]$ sudo ssh-keygen -A
ssh-keygen: generating new host keys: RSA DSA ECDSA ED25519
[slayz@router ssh]$ ls
moduli         ssh_host_dsa_key        ssh_host_ed25519_key.pub
ssh_config     ssh_host_dsa_key.pub    ssh_host_rsa_key
ssh_config.d   ssh_host_ecdsa_key      ssh_host_rsa_key.pub
sshd_config    ssh_host_ecdsa_key.pub
sshd_config.d  ssh_host_ed25519_key
```

**🌞 Tentez une nouvelle connexion au serveur**

```bash
PS C:\Users\melb3> ssh slayz@router.tp7.b1 -p 22222
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!
Someone could be eavesdropping on you right now (man-in-the-middle attack)!
It is also possible that a host key has just been changed.
The fingerprint for the ED25519 key sent by the remote host is
SHA256:DKjIPsCN8SWPK2oNxP5FEL+Mv2l3R+++kovu0XN/Abs.
Please contact your system administrator.
Add correct host key in C:\\Users\\melb3/.ssh/known_hosts to get rid of this message.
Offending ED25519 key in C:\\Users\\melb3/.ssh/known_hosts:37
Host key for [router.tp7.b1]:22222 has changed and you have requested strict checking.
Host key verification failed.
```
***Il suffit de modifier le fichier le known_hosts et de supprimer la ligne du router.tp7.b1***

```bash
PS C:\Users\melb3> ssh slayz@router.tp7.b1 -p 22222
The authenticity of host '[router.tp7.b1]:22222 ([10.7.1.254]:22222)' can't be established.
ED25519 key fingerprint is SHA256:DKjIPsCN8SWPK2oNxP5FEL+Mv2l3R+++kovu0XN/Abs.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
```
# III. Web sécurisé
## 0. Setup

**🌞 Montrer sur quel port est disponible le serveur web**

```bash
[slayz@web ~]$ sudo ss -alntpu
Netid  State   Recv-Q  Send-Q   Local Address:Port   Peer Address:Port  Process
tcp    LISTEN  0       511            0.0.0.0:80          0.0.0.0:*      users:(("nginx",pid=1709,fd=6),("nginx",pid=1708,fd=6))
```
## 1. Setup HTTPS

**🌞 Générer une clé et un certificat sur web.tp7.b1**

```bash
[slayz@web ~]$ openssl req -new -newkey rsa:2048 -days 365 -nodes -x509 -keyout server.key -out server.crt
....+...+.+.........+..+.........+.+...+......+...........+.+..+.+...............+.....+.+.....+......+..........+...+.........+...+..+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*........................+...+.....+.+......+.....+....+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.......+.+......+.....+.+...+...........+.+..+...+.......+...............+..+....+.........+..+.+..+..........+......+..+.......+........+...+....+...+...+......+........+...+...+.......+.........+......+...+...........+.+...+..+............+...+............+......+.+....................+.+....................+...+....+..+.+........+.....................+...+.+.....+................+...+...+........+............+.......+...+..+..................+.......+........+....+...........+...+.+...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
.+.+...+........+...+......+...+.......+.....+.+........+...............+....+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*......+........+.......+...+......+...+..+...+......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*.+....+..+.......+..+...+......+...............+......+...+.......+.........+......+...+..+.............+..................+..+.......+...........+....+........+..........+.....+......+.......+.....+...+.+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:
State or Province Name (full name) []:
Locality Name (eg, city) [Default City]:
Organization Name (eg, company) [Default Company Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server's hostname) []:
Email Address []:
```
```bash
[slayz@web ~]$ sudo mv server.key /etc/pki/tls/private/web.tp7.b1.key
[slayz@web ~]$ sudo mv server.crt /etc/pki/tls/certs/web.tp7.b1.crt
[slayz@web ~]$ sudo chown nginx:nginx /etc/pki/tls/private/web.tp7.b1.key
[slayz@web ~]$ sudo chown nginx:nginx /etc/pki/tls/certs/web.tp7.b1.crt
[slayz@web ~]$ sudo chmod 0400 /etc/pki/tls/private/web.tp7.b1.key
[slayz@web ~]$ sudo chmod 0444 /etc/pki/tls/certs/web.tp7.b1.crt
```
**🌞 Conf firewall**

```bash
[slayz@router ~]$ sudo firewall-cmd --add-port=443/tcp --permanent
success
[slayz@router ~]$ sudo firewall-cmd --reload
success
```

**🌞 Redémarrez NGINX**

```bash
[slayz@router ~]$ sudo systemctl restart nginx
```

**🌞 Prouvez que NGINX écoute sur le port 443/tcp**

```bash
[slayz@web ~]$ sudo ss -altnp
State  Recv-Q Send-Q Local Address:Port  Peer Address:Port
Process
LISTEN 0      511        10.7.1.12:443        0.0.0.0:*
 users:(("nginx",pid=1896,fd=6),("nginx",pid=1895,fd=6))
 ```

**🌞 Visitez le site web en https***

 ```bash
 [slayz@john ~]$ curl -k https://10.7.1.12
<h1>MEOW</h1>
```

# IV. VPN
## 3. On y va ou bien
### A. Setup serveur

**🌞 Installer le serveur Wireguard sur vpn.tp7.b1**

```bash
[slayz@vpn ~]$ sudo modprobe wireguard
[slayz@vpn ~]$ echo wireguard | sudo tee /etc/modules-load.d//wireguard.conf
wireguard
[slayz@vpn ~]$ sudo nano /etc/sysctl.conf
[slayz@vpn ~]$ sudo sysctl -p
net.ipv4.ip_forward = 1
net.ipv6.conf.all.forwarding = 1
[slayz@vpn ~]$ sudo dnf install wireguard-tools -y
Last metadata expiration check: 0:02:14 ago on Fri 24 Nov 2023 02:36:14 PM CET.
Dependencies resolved.
======================================================================== Package              Arch     Version                Repository   Size
========================================================================Installing:
 wireguard-tools      x86_64   1.0.20210914-2.el9     appstream   115 k
Upgrading:
 systemd              x86_64   252-18.el9             baseos      3.9 M
 systemd-libs         x86_64   252-18.el9             baseos      652 k
 systemd-pam          x86_64   252-18.el9             baseos      261 k
 systemd-rpm-macros   noarch   252-18.el9             baseos       50 k
 systemd-udev         x86_64   252-18.el9             baseos      1.8 M
Installing dependencies:
 systemd-resolved     x86_64   252-18.el9             baseos      361 k

Transaction Summary
========================================================================Install  2 Packages
Upgrade  5 Packages

Total download size: 7.1 M
Downloading Packages:
(1/7): wireguard-tools-1.0.20210914-2.e 485 kB/s | 115 kB     00:00
(2/7): systemd-resolved-252-18.el9.x86_ 876 kB/s | 361 kB     00:00
(3/7): systemd-rpm-macros-252-18.el9.no 255 kB/s |  50 kB     00:00
(4/7): systemd-pam-252-18.el9.x86_64.rp 1.8 MB/s | 261 kB     00:00
(5/7): systemd-udev-252-18.el9.x86_64.r 2.6 MB/s | 1.8 MB     00:00
(6/7): systemd-libs-252-18.el9.x86_64.r 2.2 MB/s | 652 kB     00:00
(7/7): systemd-252-18.el9.x86_64.rpm     11 MB/s | 3.9 MB     00:00
------------------------------------------------------------------------Total                                   4.9 MB/s | 7.1 MB     00:01
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                1/1
  Upgrading        : systemd-libs-252-18.el9.x86_64                1/12
  Running scriptlet: systemd-libs-252-18.el9.x86_64                1/12
  Upgrading        : systemd-rpm-macros-252-18.el9.noarch          2/12
  Upgrading        : systemd-pam-252-18.el9.x86_64                 3/12
  Running scriptlet: systemd-252-18.el9.x86_64                     4/12
  Upgrading        : systemd-252-18.el9.x86_64                     4/12
  Running scriptlet: systemd-252-18.el9.x86_64                     4/12
  Running scriptlet: systemd-resolved-252-18.el9.x86_64            5/12
  Installing       : systemd-resolved-252-18.el9.x86_64            5/12
  Running scriptlet: systemd-resolved-252-18.el9.x86_64            5/12
  Installing       : wireguard-tools-1.0.20210914-2.el9.x86_64     6/12
  Upgrading        : systemd-udev-252-18.el9.x86_64                7/12
  Running scriptlet: systemd-udev-252-18.el9.x86_64                7/12
  Running scriptlet: systemd-udev-252-13.el9_2.x86_64              8/12
  Cleanup          : systemd-udev-252-13.el9_2.x86_64              8/12
  Running scriptlet: systemd-udev-252-13.el9_2.x86_64              8/12
  Cleanup          : systemd-pam-252-13.el9_2.x86_64               9/12
  Cleanup          : systemd-252-13.el9_2.x86_64                  10/12
  Running scriptlet: systemd-252-13.el9_2.x86_64                  10/12
  Cleanup          : systemd-rpm-macros-252-13.el9_2.noarch       11/12
  Cleanup          : systemd-libs-252-13.el9_2.x86_64             12/12
  Running scriptlet: systemd-libs-252-13.el9_2.x86_64             12/12
  Verifying        : systemd-resolved-252-18.el9.x86_64            1/12
  Verifying        : wireguard-tools-1.0.20210914-2.el9.x86_64     2/12
  Verifying        : systemd-udev-252-18.el9.x86_64                3/12
  Verifying        : systemd-udev-252-13.el9_2.x86_64              4/12
  Verifying        : systemd-rpm-macros-252-18.el9.noarch          5/12
  Verifying        : systemd-rpm-macros-252-13.el9_2.noarch        6/12
  Verifying        : systemd-pam-252-18.el9.x86_64                 7/12
  Verifying        : systemd-pam-252-13.el9_2.x86_64               8/12
  Verifying        : systemd-libs-252-18.el9.x86_64                9/12
  Verifying        : systemd-libs-252-13.el9_2.x86_64             10/12
  Verifying        : systemd-252-18.el9.x86_64                    11/12
  Verifying        : systemd-252-13.el9_2.x86_64                  12/12

Upgraded:
  systemd-252-18.el9.x86_64       systemd-libs-252-18.el9.x86_64
  systemd-pam-252-18.el9.x86_64   systemd-rpm-macros-252-18.el9.noarch
  systemd-udev-252-18.el9.x86_64
Installed:
  systemd-resolved-252-18.el9.x86_64
  wireguard-tools-1.0.20210914-2.el9.x86_64

Complete!
```
🌞 Générer la paire de clé du serveur sur vpn.tp7.b1
```bash
wg genkey | sudo tee /etc/wireguard/server.key
2OERlvaJ1lzv4MqBRZB8ekLF7o9+mOYLHfOb650LVkc=
[slayz@vpn ~]$ sudo chmod 0400 /etc/wireguard/server.key
[slayz@vpn ~]$ sudo cat /etc/wireguard/server.key | wg pubkey | sudo tee /etc/wireguard/server.pub
oZkp/MEwwyvh0IKEK+x8kpNY7kk9MQiViikmYgcnjU8=
```

**🌞 Générer la paire de clé du client sur vpn.tp7.b1**

```bash
[slayz@vpn ~]$ sudo mkdir -p /etc/wireguard/clients
[slayz@vpn ~]$ wg genkey | sudo tee /etc/wireguard/clients/john.key
KDnSdtxPBbCihSAcuBbSXe9jCjVfyRSu2XjjY1crvVk=
[slayz@vpn ~]$ sudo chmod 0400 /etc/wireguard/server.key
[slayz@vpn ~]$ sudo cat /etc/wireguard/clients/john.key | wg pubkey | sudo tee /etc/wireguard/clients/john.pub
tee: /etc/wireguard/clients/john.pub
8oUBKP8pVQISgcFGm8XLHxMtfehmJ+BsARp5CvIA32M=
```
**🌞 Création du fichier de config du serveur sur vpn.tp7.b1**

```bash
[slayz@vpn ~]$ sudo cat /etc/wireguard/wg0.conf
[sudo] password for slayz:
[Interface]
Address = 10.107.1.0/24
SaveConfig = false
PostUp = firewall-cmd --zone=public --add-masquerade
PostUp = firewall-cmd --add-interface=wg0 --zone=public
PostDown = firewall-cmd --zone=public --remove-masquerade
PostDown = firewall-cmd --remove-interface=wg0 --zone=public
ListenPort = 13337
PrivateKey = 2OERlvaJ1lzv4MqBRZB8ekLF7o9+mOYLHfOb650LVkc=

[Peer]
PublicKey = 8oUBKP8pVQISgcFGm8XLHxMtfehmJ+BsARp5CvIA32M=
AllowedIPs = 10.107.1.11/32
```

**🌞 Démarrez le serveur VPN sur vpn.tp7.b1**

```bash
[slayz@vpn ~]$ sudo systemctl start wg-quick@wg0.service
[sudo] password for slayz:
[slayz@vpn ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:45:42:6e brd ff:ff:ff:ff:ff:ff
    inet 10.7.1.101/24 brd 10.7.1.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe45:426e/64 scope link
       valid_lft forever preferred_lft forever
3: wg0: <POINTOPOINT,NOARP,UP,LOWER_UP> mtu 1420 qdisc noqueue state UNKNOWN group default qlen 1000
    link/none
    inet 10.107.1.0/24 scope global wg0
       valid_lft forever preferred_lft forever
[slayz@vpn ~]$ sudo wg show
interface: wg0
  public key: ZEF9a3IueCF5HkiThcri1RJe09AE7YKnLjxCYiNiH3E=
  private key: (hidden)
  listening port: 13337

peer: DVR4MJBA6f2qn4CY12Xh6BDC3vG9cYar6jgk2Wi67gc=
  allowed ips: 10.107.1.11/32
```
**🌞 Ouvrir le bon port firewall**

```bash
[slayz@localhost ~]$ sudo ss -alntpu
Netid  State   Recv-Q  Send-Q   Local Address:Port    Peer Address:Port Process
udp    UNCONN  0       0              0.0.0.0:13337        0.0.0.0:*
```

```bash
[slayz@vpn ~]$ sudo firewall-cmd --add-port=13337/udp --permanent
success
[slayz@vpn ~]$ sudo firewall-cmd --reload
success
```
### B. Setup client
**🌞 On installe les outils Wireguard sur john.tp7.b1**
```bash
[slayz@john ~]$ sudo dnf install wireguard-tools
[sudo] password for slayz:
Rocky Linux 9 - BaseOS                   12 kB/s | 4.1 kB     00:00
Rocky Linux 9 - AppStream               9.3 kB/s | 4.5 kB     00:00
Rocky Linux 9 - Extras                  9.1 kB/s | 2.9 kB     00:00
Dependencies resolved.
========================================================================
 Package              Arch     Version                Repository   Size
========================================================================
Installing:
 wireguard-tools      x86_64   1.0.20210914-2.el9     appstream   115 k
Upgrading:
 systemd              x86_64   252-18.el9             baseos      3.9 M
 systemd-libs         x86_64   252-18.el9             baseos      652 k
 systemd-pam          x86_64   252-18.el9             baseos      261 k
 systemd-rpm-macros   noarch   252-18.el9             baseos       50 k
 systemd-udev         x86_64   252-18.el9             baseos      1.8 M
Installing dependencies:
 systemd-resolved     x86_64   252-18.el9             baseos      361 k

Transaction Summary
========================================================================
Install  2 Packages
Upgrade  5 Packages

Total download size: 7.1 M
Is this ok [y/N]: y
Downloading Packages:
Rocky Linux 9 - 160% [=========================-] 8.7 kB/s | 4.7 kB     (1/7): systemd-resolved-252-18.el9.x86_ 1.5 MB/s | 361 kB     00:00
(2/7): wireguard-tools-1.0.20210914-2.e 416 kB/s | 115 kB     00:00
(3/7): systemd-rpm-macros-252-18.el9.no 912 kB/s |  50 kB     00:00
(4/7): systemd-udev-252-18.el9.x86_64.r 3.9 MB/s | 1.8 MB     00:00
(5/7): systemd-pam-252-18.el9.x86_64.rp 1.4 MB/s | 261 kB     00:00
(6/7): systemd-libs-252-18.el9.x86_64.r 3.6 MB/s | 652 kB     00:00
(7/7): systemd-252-18.el9.x86_64.rpm    6.0 MB/s | 3.9 MB     00:00
------------------------------------------------------------------------
Total                                   4.2 MB/s | 7.1 MB     00:01
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
  Preparing        :                                                1/1
  Upgrading        : systemd-libs-252-18.el9.x86_64                1/12
  Running scriptlet: systemd-libs-252-18.el9.x86_64                1/12
  Upgrading        : systemd-rpm-macros-252-18.el9.noarch          2/12
  Upgrading        : systemd-pam-252-18.el9.x86_64                 3/12
  Running scriptlet: systemd-252-18.el9.x86_64                     4/12
  Upgrading        : systemd-252-18.el9.x86_64                     4/12
  Running scriptlet: systemd-252-18.el9.x86_64                     4/12
  Running scriptlet: systemd-resolved-252-18.el9.x86_64            5/12
  Installing       : systemd-resolved-252-18.el9.x86_64            5/12
  Running scriptlet: systemd-resolved-252-18.el9.x86_64            5/12
  Installing       : wireguard-tools-1.0.20210914-2.el9.x86_64     6/12
  Upgrading        : systemd-udev-252-18.el9.x86_64                7/12
  Running scriptlet: systemd-udev-252-18.el9.x86_64                7/12
  Running scriptlet: systemd-udev-252-13.el9_2.x86_64              8/12
  Cleanup          : systemd-udev-252-13.el9_2.x86_64              8/12
  Running scriptlet: systemd-udev-252-13.el9_2.x86_64              8/12
  Cleanup          : systemd-pam-252-13.el9_2.x86_64               9/12
  Cleanup          : systemd-252-13.el9_2.x86_64                  10/12
  Running scriptlet: systemd-252-13.el9_2.x86_64                  10/12
  Cleanup          : systemd-rpm-macros-252-13.el9_2.noarch       11/12
  Cleanup          : systemd-libs-252-13.el9_2.x86_64             12/12
  Running scriptlet: systemd-libs-252-13.el9_2.x86_64             12/12
  Verifying        : systemd-resolved-252-18.el9.x86_64            1/12
  Verifying        : wireguard-tools-1.0.20210914-2.el9.x86_64     2/12
  Verifying        : systemd-udev-252-18.el9.x86_64                3/12
  Verifying        : systemd-udev-252-13.el9_2.x86_64              4/12
  Verifying        : systemd-rpm-macros-252-18.el9.noarch          5/12
  Verifying        : systemd-rpm-macros-252-13.el9_2.noarch        6/12
  Verifying        : systemd-pam-252-18.el9.x86_64                 7/12
  Verifying        : systemd-pam-252-13.el9_2.x86_64               8/12
  Verifying        : systemd-libs-252-18.el9.x86_64                9/12
  Verifying        : systemd-libs-252-13.el9_2.x86_64             10/12
  Verifying        : systemd-252-18.el9.x86_64                    11/12
  Verifying        : systemd-252-13.el9_2.x86_64                  12/12

Upgraded:
  systemd-252-18.el9.x86_64       systemd-libs-252-18.el9.x86_64
  systemd-pam-252-18.el9.x86_64   systemd-rpm-macros-252-18.el9.noarch
  systemd-udev-252-18.el9.x86_64
Installed:
  systemd-resolved-252-18.el9.x86_64
  wireguard-tools-1.0.20210914-2.el9.x86_64

Complete!
```
**🌞 Supprimez votre route par défaut**
```bash
[slayz@john ~]$ sudo ip route del default via 10.7.1.254 dev enp0s3
[slayz@john ~]$ sudo systemctl restart NetworkManager
[slayz@john ~]$ ip r s
10.7.1.0/24 dev enp0s3 proto kernel scope link src 10.7.1.11 metric 100
[slayz@john ~]$ ping 8.8.8.8
ping: connect: Network is unreachable
```

**🌞 Configurer un fichier de conf sur john.tp7.b1**

```bash
PS C:\Users\melb3> ssh slayz@10.7.1.11
Last login: Sat Nov 25 15:12:05 2023
[slayz@john ~]$ cd wireguard/
[slayz@john wireguard]$ cd
[slayz@john ~]$ sudo cat wireguard/john.conf
[sudo] password for slayz:
[Interface]
Address = 10.107.1.11/24
PrivateKey = KDnSdtxPBbCihSAcuBbSXe9jCjVfyRSu2XjjY1crvVk=

[Peer]
PublicKey = oZkp/MEwwyvh0IKEK+x8kpNY7kk9MQiViikmYgcnjU8=
AllowedIPs = 0.0.0.0/0
Endpoint = 10.7.1.101:13337
```
**🌞 Lancer la connexion VPN**

```bash
[slayz@john ~]$ cd wireguard/
[slayz@john wireguard]$ wg-quick up ./john.conf
Warning: `/home/slayz/wireguard/john.conf' is world accessible
[#] ip link add john type wireguard
[#] wg setconf john /dev/fd/63
[#] ip -4 address add 10.107.1.11/24 dev john
[#] ip link set mtu 1420 up dev john
[#] wg set john fwmark 51820
[#] ip -4 route add 0.0.0.0/0 dev john table 51820
[#] ip -4 rule add not fwmark 51820 table 51820
[#] ip -4 rule add table main suppress_prefixlength 0
[#] sysctl -q net.ipv4.conf.all.src_valid_mark=1
[#] nft -f /dev/fd/63
```
**🌞 Vérifier la connexion**
- sur john.tp7.b1
    - ping 1.1.1.1 et ping ynov.com et traceroute 1.1.1.1 :
```bash
[slayz@john ~]$ ping 1.1.1.1
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
64 bytes from 1.1.1.1: icmp_seq=1 ttl=52 time=27.4 ms
64 bytes from 1.1.1.1: icmp_seq=2 ttl=52 time=27.8 ms
^C
--- 1.1.1.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 27.364/27.599/27.835/0.235 ms
[slayz@john ~]$ ping ynov.com
PING ynov.com (104.26.11.233) 56(84) bytes of data.
64 bytes from 104.26.11.233 (104.26.11.233): icmp_seq=1 ttl=52 time=26.1 ms
64 bytes from 104.26.11.233 (104.26.11.233): icmp_seq=2 ttl=52 time=27.4 ms
^C
--- ynov.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 26.112/26.732/27.352/0.620 ms
[slayz@john ~]$ traceroute 1.1.1.1
traceroute to 1.1.1.1 (1.1.1.1), 30 hops max, 60 byte packets
 1  10.107.1.0 (10.107.1.0)  3.314 ms  6.459 ms  5.385 ms
 2  * * *
 3  10.0.3.2 (10.0.3.2)  20.530 ms  19.490 ms  18.490 ms
 4  * * *
 5  * * *
 6  * * *
 7  * * *
 8  * * *
 9  * * *
10  * * *
11  * * *
12  * * *
13  * * *
14  * * *
15  * * *
16  * * *
17  * * *
18  * * *
19  * * *
20  * * *
21  * * *
22  * * *
23  * * *
24  * * *
25  * * *
26  * * *
27  * * *
28  * * *
29  * * *
30  * * *
```
- ip a
```bash
[slayz@john ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:5b:5e:7c brd ff:ff:ff:ff:ff:ff
    inet 10.7.1.11/24 brd 10.7.1.255 scope global noprefixroute enp0s3
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe5b:5e7c/64 scope link
       valid_lft forever preferred_lft forever
3: john: <POINTOPOINT,NOARP,UP,LOWER_UP> mtu 1420 qdisc noqueue state UNKNOWN group default qlen 1000
    link/none
    inet 10.107.1.11/24 scope global john
       valid_lft forever preferred_lft forever
```
- wg show
```bash
[slayz@john ~]$ sudo wg show
interface: john
  public key: DVR4MJBA6f2qn4CY12Xh6BDC3vG9cYar6jgk2Wi67gc=
  private key: (hidden)
  listening port: 59992
  fwmark: 0xca6c

peer: ZEF9a3IueCF5HkiThcri1RJe09AE7YKnLjxCYiNiH3E=
  endpoint: 10.7.1.101:13337
  allowed ips: 0.0.0.0/0
  latest handshake: 12 seconds ago
  transfer: 5.59 KiB received, 13.07 KiB sent
```
- sur vpn.tp7.b1
    - wg show 
```bash
[slayz@vpn ~]$ sudo wg show
[sudo] password for slayz:
interface: wg0
  public key: ZEF9a3IueCF5HkiThcri1RJe09AE7YKnLjxCYiNiH3E=
  private key: (hidden)
  listening port: 13337

peer: DVR4MJBA6f2qn4CY12Xh6BDC3vG9cYar6jgk2Wi67gc=
  endpoint: 10.7.1.11:59992
  allowed ips: 10.107.1.11/32
  latest handshake: 2 minutes, 7 seconds ago
  transfer: 13.84 KiB received, 6.55 KiB sent
```

[Clique ici pour voir le trafic depuis le serveur VPN entrer](tp7_vpn_in.pcapng)

[Clique ici pour voir le trafic depuis le serveur VPN sorti](tp7_vpn_out.pcapng)