ip choisi : 10.10.10.33/30 et 10.10.10.34/30
adresse de reseau 10.10.10.32
adresse de broadcast 10.10.10.35

```powershell
New-NetIPAddress –InterfaceIndex 22 –IPAddress 10.10.10.33 –PrefixLength 30
```
Reponse 
ps c user macime > 
PS C:\WINDOWS\system32> New-NetIPAddress -InterfaceIndex 22 -IPAddress 10.10.10.33 -PrefixLength 30


IPAddress         : 10.10.10.33
InterfaceIndex    : 22
InterfaceAlias    : Ethernet
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 30
PrefixOrigin      : Manual
SuffixOrigin      : Manual
AddressState      : Tentative
ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
SkipAsSource      : False
PolicyStore       : ActiveStore

IPAddress         : 10.10.10.33
InterfaceIndex    : 22
InterfaceAlias    : Ethernet
AddressFamily     : IPv4
Type              : Unicast
PrefixLength      : 30
PrefixOrigin      : Manual
SuffixOrigin      : Manual
AddressState      : Invalid
ValidLifetime     : Infinite ([TimeSpan]::MaxValue)
PreferredLifetime : Infinite ([TimeSpan]::MaxValue)
SkipAsSource      : False
PolicyStore       : PersistentStore

🌞 Prouvez que la connexion est fonctionnelle entre les deux machines
```powershell
ping 10.10.10.34
```
reponse
PS C:\Users\melb3> ping 10.10.10.34

Envoi d’une requête 'Ping'  10.10.10.34 avec 32 octets de données :
Réponse de 10.10.10.34 : octets=32 temps=3 ms TTL=128
Réponse de 10.10.10.34 : octets=32 temps=3 ms TTL=128
Réponse de 10.10.10.34 : octets=32 temps=4 ms TTL=128
Réponse de 10.10.10.34 : octets=32 temps=4 ms TTL=128

Statistiques Ping pour 10.10.10.34:
    Paquets : envoyés = 4, reçus = 4, perdus = 0 (perte 0%),
Durée approximative des boucles en millisecondes :
    Minimum = 3ms, Maximum = 4ms, Moyenne = 3ms



[ping](./ping%20request.pcapng)

```powershell
arp -a 10.10.10.33
```
Reponse
PS C:\Users\melb3> arp -a 10.10.10.34

Interface : 10.10.10.33 --- 0x16
  Adresse Internet      Adresse physique      Type
  10.10.10.34           7c-57-58-68-83-12     dynamique
arp -a

Interface : 10.10.10.33 --- 0x16
  Adresse Internet      Adresse physique      Type
  10.10.10.34           7c-57-58-68-83-12     dynamique
  10.10.10.35           ff-ff-ff-ff-ff-ff     statique
  224.0.0.22            01-00-5e-00-00-16     statique
  224.0.0.251           01-00-5e-00-00-fb     statique
  239.255.255.250       01-00-5e-7f-ff-fa     statique

arp - a 10.10.1035 

reponse
PS C:\Users\melb3> arp -a 10.10.10.35

Interface : 10.10.10.33 --- 0x16
  Adresse Internet      Adresse physique      Type
  10.10.10.35           ff-ff-ff-ff-ff-ff     statique

10.33.51.254          7c-5a-1c-cb-fd-a4     dynamique

netsh interface IP delete arpcache

Interface : 10.10.10.33 --- 0x16
  Adresse Internet      Adresse physique      Type
  10.10.10.34           7c-57-58-68-83-12     dynamique
  224.0.0.22            01-00-5e-00-00-16     statique