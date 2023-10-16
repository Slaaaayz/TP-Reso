# I. Exploration locale en solo
## 1. Affichage d'informations sur la pile TCP/IP locale
**🌞 Affichez les infos des cartes réseau de votre PC**
```powershell
ipconfig /all
```
**Réponse :** 
```powershell
PS C:\Users\Maxime> ipconfig /all
Carte réseau sans fil Wi-Fi :
   Description. . . . . . . . . . . . . . : MediaTek Wi-Fi 6 MT7921 Wireless LAN Card
   Adresse physique . . . . . . . . . . . : 34-6F-24-93-79-AD
   Adresse IPv4. . . . . . . . . . . . . .: 10.33.50.245
Carte Ethernet Ethernet :
   Description. . . . . . . . . . . . . . : Realtek PCIe GbE Family Controller
   Adresse physique . . . . . . . . . . . : 50-EB-F6-30-83-17
```
- Adresse IP : non

**🌞 Affichez votre gateway**   

```powershell
ipconfig
```
**Réponse :**
```powershell
PS C:\Users\Maxime> ipconfig
   Passerelle par défaut. . . . . . . . . : 10.33.50.254 
```
**🌞 Déterminer la MAC de la passerelle** 
```powershell
arp -a 192.168.1.254
```
**Réponse :**
```powershell
Adresse Internet      Adresse physique      Type
  10.33.50.254         20-66-cf-1a-85-bc     dynamique
```
**🌞 Trouvez comment afficher les informations sur une carte IP (change selon l'OS)**  

[Panneau de configuration\Réseau et Internet\Centre Réseau et partage] Sélectionner le wifi puis cliquer sur "Détails"

- Adresse IP : 10.33.50.245
- Adresse MAC : 34-6F-24-93-79-AD 
- Gateway : 10.33.50.254

## 2. Modifications des informations 
### A. Modification d'adresse IP (part 1)

![Changement IP](<image/changement ip.PNG>)

**Il est possible que vous perdiez l'accès internet.** Que ce soit le cas ou non, expliquez pourquoi c'est possible de perdre son accès internet en faisant cette opération.

**Réponse :** C'est possible de perdre l'accès car l'adresse IP est l'adresse de la machine sur le réseau local. Si l'adresse IP est changée, la machine ne sera plus reconnue sur le réseau local et donc ne pourra plus accéder à internet. Pour rester dans le même réseau, on peut modifier que le dernier octet pour rester dans le même réseau que la passerelle.

# II. Exploration locale en duo

ABSENT (pas de binôme) 

# III. Manipulations d'autres outils/protocoles côté client
## 1. DHCP d'autres outils/protocoles côté client
```powershell
ipconfig /all
```
**Reponse :**
```powershell
PS C:\Users\Maxime> ipconfig /all
Serveur DHCP . . . . . . . . . . . . . : 192.168.1.254
Bail expirant. . . . . . . . . . . . . : eudi 12 octobre 2023 03:28:22
```
## 2. DNS
```powershell
ipconfig /all
```
**Réponse :**
```powershell
PS C:\Users\Maxime> ipconfig /all
Carte réseau sans fil Wi-Fi :
Serveurs DNS. . .  . . . . . . . . . . : 192.168.1.254
                                         8.8.8.8
```
- **Un lookup pour google.com**
```powershell
nslookup google.com
```
**Réponse :** 
```powershell
PS C:\Users\Maxime> nslookup google.com 8.8.8.8
Serveur :   dns.google
Address:  8.8.8.8

Réponse ne faisant pas autorité :
Nom :    google.com
Addresses:  2a00:1450:4007:818::200e
          216.58.214.174
```
- **Puis pour Ynov :** 
```powershell
nslookup ynov.com
```
**Réponse :**
```
PS C:\Users\Maxime> nslookup ynov.com 8.8.8.8
Serveur :   dns.google
Address:  8.8.8.8

Réponse ne faisant pas autorité :
Nom :    ynov.com
Addresses:  2606:4700:20::681a:be9
          2606:4700:20::681a:ae9
          2606:4700:20::ac43:4ae2
          104.26.11.233
          104.26.10.233
          172.67.74.226
```

Les deux domaines utilisent le même DNS 8.8.8.8 pour faire des requêtes

l'adresse IP du serveur à qui on vient d'effectuer ces requêtes est 8.8.8.8
- **Un lookup pour l'adresse : 231.34.113.12**
```powershell
nslookup 231.34.113.12
```
**Reponse :** 
```
PS C:\Users\Maxime> nslookup 231.34.113.12 8.8.8.8
Serveur :   dns.google
Address:  8.8.8.8

*** dns.google ne parvient pas à trouver 231.34.113.12 : Non-existent domain
```
Le DNS de google ne connait pas le nom du domaine associé à cette adresse IP
- **Un lookup pour l'adresse : 231.34.113.12**
```powershell
nslookup 78.34.2.17
```
**Reponse :**
```powershell
PS C:\Users\Maxime> nslookup 78.34.2.17 8.8.8.8
Serveur :   dns.google
Address:  8.8.8.8

Nom :    cable-78-34-2-17.nc.de
Address:  78.34.2.17
```

Le DNS de google connait le nom du domaine associé à cette adresse IP qui est : cable-78-34-2-17.nc.de

# IV. Wireshark
