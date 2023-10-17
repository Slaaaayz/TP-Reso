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



[yoyoyoyoy](./ping%20request.pcapng)