# 2025-11-06 uppdatering

Problem med att få upp det här på github, försöker igen.

# 2025-11-06
Jag börjar med att konfigurera switchar på GBG-sidan, se kod längre ner.
Vi skapar alltså alla vlan, 10 för kontor, 20 för gäst, 99 för admin samt 999 som ska användas senare för övrig trafik.

Konfigurerar trunkportarna som ska gå mellan switchar och routrar så att alla vlan kan kommuniceras ordentligt.
Efter det här så installerar jag end points och ställer in dhcp på routerns olika subinterfaces. Nu öppnar jag portar på switcharna till end points och konfigurerar dem allt efter som för att hålla ordning på de olika vlanen.

Jag gör samma vända på STH-sidan så vi har en spegelbild, mer eller mindre.

Nu börjar det vara dags för att skapa och konfigurera den virtuella tunneln vi ska ha. Efter det är klart så gör vi två statiska IP routes.

Nu är allt på plats, det  går bra att pinga mellan de olika siterna. 



Kod för olika ändamål till switchar och router:

    vlan 10
    name Office
    vlan 20
    name Guest
    vlan 99
    name Admin
    vlan 999
    name Blackhole
    interface range fa0/1-24
    switchport access vlan 999
    description Not in use
    shutdown
    interface range gig0/1-2
    switchport access vlan 999
    description Not in use
    shutdown
    interface gig0/1
    switchport mode trunk
    switchport trunk native vlan 100
    switchport trunk allowed vlan 10-100
    no shutdown

    ip dhcp pool vlan10
    default-router 172.16.2.1
    network 172.16.2.0 255.255.255.192

    ip dhcp excluded-address 172.16.2.129 172.16.2.133

    interface tunnel 1
    ip address 192.168.255.2 255.255.255.252
    tunnel destination 80.80.80.2
    tunnel mode gre ip
    tunnel source Serial 0/3/0

    ip route 10.0.1.0 255.255.255.0 192.168.255.1
    ip route 0.0.0.0 0.0.0.0 Serial 0/3/0