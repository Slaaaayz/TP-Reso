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
- Adresse IP pour la carte Ethernet : non

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
**🌞 Utilisez l'interface graphique de votre OS pour changer d'adresse IP :**
![Changement IP](<image/changement ip.PNG>)

**🌞 Il est possible que vous perdiez l'accès internet.** Que ce soit le cas ou non, expliquez pourquoi c'est possible de perdre son accès internet en faisant cette opération.

**Réponse :** C'est possible de perdre l'accès car l'adresse IP est l'adresse de la machine sur le réseau local. Si l'adresse IP est changée, la machine ne sera plus reconnue sur le réseau local et donc ne pourra plus accéder à internet. Pour rester dans le même réseau, on peut modifier que le dernier octet pour rester dans le même réseau que la passerelle.

# II. Exploration locale en duo
## 3. Modification d'adresse IP
**🌞 Modification de nos adresses IP en utilisant le GUI (windows)**  
Adresse IP choisis :   10.10.10.33 et 10.10.10.34
![Alt text](<image/changement ipppp.PNG>)  
**🌞 Vérifier à l'aide d'une commande que votre IP a bien été changée**
```powershell
ipconfig /all
```
**Réponse :**
```powershell
PS C:\Users\Maxime>ipconfig /all
Carte Ethernet Ethernet :
     Adresse IPv4. . . . . . . . . . . . . .: 10.10.10.34 (préféré)
```
**🌞 Vérifier que les deux machines se joignent**
```powershell
ping 10.10.10.33
```
**Réponse :**
```powershell
PS C:\Users\Maxime> ping 10.10.10.33
Statistiques Ping pour 10.10.10.33:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 3ms, Maximum = 6ms, Moyenne = 4ms
```
**🌞 Déterminer l'adresse MAC de votre correspondant**
```powershell
arp -a 10.10.10.33
```
**Réponse :**
```powershell
Interface : 10.10.10.33 
    Adresse Internet      Adresse physique      Type
     10.10.10.32           e4-a8-df-d1-98-ca     dynamique
```
**🌞 sur le PC serveur**  
```powershell
.\nc.exe -l -p 8888
```
**Réponse :**  
```powershell
PS C:\Users\Maxime\Documents\netcat-1.11> .\nc.exe -l -p 8888
```
*Il faut attendre que le client se connecte car aucune réponse*  
**🌞 sur le PC client**
```powershell
.\nc.exe 10.10.10.33- 8888
```
**Réponse :**
Chat ouvert aves l'autre pc 
**🌞 Visualiser la connexion en cours**
```powershell
netstat -a -n -b | Select-String 8888 -Context 0,1
```
**Réponse :**
a mettre  

**🌞 Pour aller un peu plus loin**
```powershell
.\nc.exe -l -p 8888 -s 10.10.10.34
```
**Réponse :**  
a mettre 
## 5. Firewall
**🌞 Activez et configurez votre firewall**  
a mettre  
## 6. Utilisation d'un des deux comme gateway
**🌞Tester l'accès internet**   
a mettre  
**🌞 Prouver que la connexion Internet passe bien par l'autre PC**   
a mettre  
🌞 **Exploration du DHCP**

`>> ipconfig /all`

> Résultat :

     Serveur DHCP . . . . . . . . . . . . . : 10.33.51.254
      Bail obtenu. . . . . . . . . . . . . . : lundi 16 octobre 2023 13:43:41
   Bail expirant. . . . . . . . . . . . . : mardi 17 octobre 2023 13:43:39

🌞 **Trouver l'adresse IP du serveur DNS que connaît votre ordinateur**

`>> ipconfig /all`

> Résultat :

    Serveurs DNS. . .  . . . . . . . . . . : 10.33.10.2
    8.8.8.8

🌞 **NSLOOKUP**

`>> nslookup google.com 8.8.8.8`

> Résultat :

    Serveur :   dns.google
    Address:  8.8.8.8

    Réponse ne faisant pas autorité :
    Nom :    google.com
    Addresses:  2a00:1450:4007:818::200e
            142.250.179.110

`>> nslookup 231.34.113.12 8.8.8.8`

> Résultat :

    *** dns.google ne parvient pas à trouver 231.34.113.12 : Non-existent domain

`>> nslookup 78.34.2.17 8.8.8.8`

> Résultat :

    Serveur :   dns.google
    Address:  8.8.8.8

    Réponse ne faisant pas autorité :
    Nom :    google.com
    Addresses:  2a00:1450:4007:818::200e
            142.250.179.110
 

# III. Manipulations d'autres outils/protocoles côté client
## 1. DHCP d'autres outils/protocoles côté client
**🌞Exploration du DHCP, depuis votre PC**
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
🌞**Trouver l'adresse IP du serveur DNS que connaît votre ordinateur**
```powershell
ipconfig /all
```
**Réponse :**
```powershell
PS C:\Users\Maxime> ipconfig /all
Carte réseau sans fil Wi-Fi :
Serveurs DNS. . .  . . . . . . . . . . : 10.33.50.254
                                         8.8.8.8
```
**🌞 Utiliser, en ligne de commande l'outil nslookup**
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
L'adresse IP du serveur à qui on vient d'effectuer ces requêtes est 8.8.8.8
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
