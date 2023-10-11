# I. Exploration locale en solo
## 1. Affichage d'informations sur la pile TCP/IP locale

```powershell
ipconfig /all
```

**Informations sur la carte réseau sans fil Wi-Fi :**  
- Nom : MediaTek Wi-Fi 6 MT7921 Wireless LAN Card
- Adresse physique : 34-6F-24-93-79-AD
- Adresse IP: 192.168.1.54

**Informations sur la carte réseau Ethernet :** 

- Nom : Realtek PCIe GbE Family Controller
- Adresse physique : 50-EB-F6-30-83-17
- Adresse IP: non

**Adresse IP de la passerelle par défaut :**   

```powershell
ipconfig
```
- Adresse IP : 192.168.1.254 

**Adresse MAC de la passerelle par défaut :** 
```powershell
arp -a 192.168.1.254
```
- Adresse MAC : 20-66-cf-1a-85-bc

**Informations sur une carte IP en utilisant l'interface graphique :**  

[Panneau de configuration\Réseau et Internet\Centre Réseau et partage] Sélectionner le wifi puis cliquer sur "Détails"

- Adresse IP : 192.168.1.54
- Adresse MAC : 34-6F-24-93-79-AD 
- Gateway : 192.168.1.54

## 2. Modifications des informations 
### A. Modification d'adresse IP (part 1)

![Changement ip](<changement ip.PNG>)

**Il est possible que vous perdiez l'accès internet.** Que ce soit le cas ou non, expliquez pourquoi c'est possible de perdre son accès internet en faisant cette opération.

**Réponse :** C'est possible de perdre l'accès car l'adresse IP est l'adresse de la machine sur le réseau local. Si l'adresse IP est changée, la machine ne sera plus reconnue sur le réseau local et donc ne pourra plus accéder à internet. Pour rester dans le même réseau, on peut modifier que le dernier octet pour rester dans le même réseau que la passerelle.

# II. Exploration locale en duo

ABSENT (pas de binôme) 

# III. Manipulations d'autres outils/protocoles côté client
## 1. DHCP d'autres outils/protocoles côté client
```powershell
ipconfig /all
```
- Adresse IP du serveur DHCP : 192.168.1.254
- Bail d'expiration DHCP : jeudi 12 octobre 2023 03:28:22
## 2. DNS
```powershell
ipconfig /all
```
- Serveur DNS que connait l'ordinateur : 192.168.1.54 et fd0f:ee:b0::1
```powershell
nslookup google.com
```
- Serveur : Unknow
- Address: 192.168.1.254
- Nom : google.com
- Address: 2a00:1450:4007:80d::200e et 172.217.20.174
```powershell
nslookup ynov.com
```
- Serveur : Unknow
- Address: 192.168.1.254
- Nom : ynov.com
- Address : 2606:4700:20::681a:be9 / 2606:4700:20::681a:ae9 / 2606:4700:20::ac43:4ae2 / 104.26.10.233 / 172.67.74.226 / 104.26.11.233  

Les deux domaines utilisent le même DNS 172.168.1.254 pour faire des requêtes

l'adresse IP du serveur à qui on vient d'effectuer ces requêtes est 172.168.1.254
```powershell
nslookup 231.34.113.12
```
- Reponse : *** dns.google ne parvient pas à trouver 231.34.113.12 : Non-existent domain

Le DNS de google ne connait pas le nom du domaine associé à cette adresse IP
```powershell
nslookup 78.34.2.17
```
- Nom : cable-78-34-2-17.nc.de
- Address: 78.34.2.17

Le DNS de google connait le nom du domaine associé à cette adresse IP qui est : cable-78-34-2-17.nc.de

# IV. Wireshark

**Un ping entre vous et la passerelle du réseau** 

![Ping passerelle](ping.PNG)