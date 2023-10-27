# TP4 : DHCP
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

