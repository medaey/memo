## Tache a faire pour init un routeur

les tâches suivantes doivent être traitées lors de la configuration des paramètres initiaux d'un routeur.
1. Configurer le nom de l'appareil.
2. Sécuriser le mode d'exécution privilégié.
3. Sécuriser le mode d'exécution utilisateur
4. Sécuriser Telnet à distance / accès SSH
5. Sécuriser tous les mots de passe dans le fichier de configuration.
6. Fournir un avertissement légal
7. Enregistrer la configuration.
```
! --- Mise en place ---
en
conf t

!! - Configurer le nom de l'appareil !!
hostname S1

!! Securiser le mode exeution privilegier !!
line console 0
password cisco
login
exit

!! Securiser le mode execution utilisateur !!
line vty 0 4
password cisco
login

!! Securiser acces a distance par ssh !!
transport input ssh
exit

!! Securiser les mot de passe dans le fichier config !!
service password-encryption

!! Avertisement legal !!
banner motd "Acces reserver aux personnes autorise uniquement. Les contrevenants seront poursuivis en justice."

!! enregistrer le config !!
end
wr

! --- Verification ---
show run
```

## Nommer de l'appareil.
```
! --- Mise en place ---
en
conf t
host S2
exit
wr

! --- Verification ---
```

## Réinitialiser le configuration de l'appareil.

```
! --- Mise en place ---
en

!! supprime le sifhcier startup-config !!
erase startup-config
Y

!! changer le configuration initial !!
reload
N
Y

! --- Verification ---
```

## Ajouter une adresse ip
```
! --- Mise en place ---
en
conf t

!! choisir interface !!
interface fa 0/0

!! definir adresse ip !!
ip address 192.168.50.254 255.255.255.0
no shutdown
end
wr

! --- Verification ---
show int fa 0/0
```

## Sécurisez l’accès des commutateurs

```
! --- Mise en place ---
en
conf t
enable secret cisco
line console 0
password cisco
login
exit
line vty 0 4
password cisco
login
transport input ssh
exit
service password-encryption
banner motd "Acces reserver aux personnes autorise uniquement. Les contrevenants seront poursuivis en justice."
end
wr

! --- Verification ---
```

## SSH uniquement
```
! --- Mise en place ---
en
conf t
line vty 0 15
transport input ssh
end
wr

! --- Verification ---
```

## Désactiver port
```
! --- Mise en place ---
en
conf t
interface Fa0/1
shutdown
end
wr

! --- Verification ---
show ip interface brief
```

## Filtrage Mac par port

```
! --- Mise en place ---
!! Choisir les port !!
en
conf t
interface range Fa0/1 - 24

!! active le port security !!
switchport mode access
switchport port-security
switchport port-security maximum 1
switchport port-security mac-address sticky
switchport port-security violation restrict

!! reactive le port !!
sh
no sh

!! Sauvegarde le configuration !!
end
wr

! --- Verification ---
show ip interface brief
show port-security interface Fa0/1
```

## syslog Log sur un serveur 

```
! --- Mise en place ---
en
conf t
loggin 192.168.50.100
loggin trap debugging
end
wr
! --- Verification ---
show logging
```

## LACP agrégation de lien

```
! --- Mise en place ---
en
conf t
int range fa0/23 - 24
channel-protocol lacp
channel-group 1 mode active
int port-channel 1
switchport mode trunk
no sh
exit
int range fa0/23 - 24
no sh
end
wr

! --- Verification ---
sh etherchannel
```

## Sauvegarde TFTP
```
! --- Mise en place ---
en
wr
copy running-config tftp:
192.168.112.100
Rt-Paris2-confg

! --- Verification ---
```

## Passer un port en mode trunk
```
! --- Mise en place ---
en
conf t
int range fa0/1 - 2
switchport mode trunk
end
wr

! --- Verification ---
show interfaces status
```

## Nom de domaine
```
! --- Mise en place ---
en
conf t
ip domaine-name cossu.tech
end
wr

! --- Verification ---
show run
```

## SSH avec certificat mod 512

```
! --- Mise en place ---
en
conf t
enable secret cisco
line console 0
password cisco
login
exit
hostname routeur-cisco
ip domain-name cossu.tech
crypto key generate rsa general-keys modulus 512
ip ssh version 2
service password-encryption
username admin password 0 cisco
end
wr

! --- Verification ---
show ip ssh
```

## Vlan Création
```
! --- Mise en place ---
en
conf t
vlan 10
name VLAN10
exit
int range fa 0/1 - 12
switchport mode access
switchport access vlan 10
no sh
end
wr

! --- Verification ---
show vlan
```

## Vlan création d'interface de route 
```
! --- Mise en place ---
en
conf t
int vlan 10
no sh
ip address 192.168.0.254 255.255.255.0
end
wr

! --- Verification ---
```

## Vlan liaison de vlan

```
! --- Mise en place ---
en
conf t
int fa0/1
switchport mode trunk
switchport trunk allowed vlan 20,30,99
switchport trunk native vlan 99
no shutdown
end
wr

! --- Verification ---
show vlan
```

## Spanning Tree
```
! --- Mise en place ---
en
conf t
spanning-tree mode rapid-pvst
end
wr

! --- Verification ---
sh spanning-tree
```

## Spanning Tree fixer le switch root

En générale les Cœur de réseau est le root, quand au 2ème cœur il est en désigner.

```
! --- Mise en place ---
en
conf t

! definit le root pour le vlan de 1 a 100 !
spanning-tree vlan 1-100 root primary

! --- Verification --- 
show spanning-tree vlan 4
```

## Dhcp ajouter le DNS
<https://www.manageengine.com/fr/network-configuration-manager/configlets/configure-dhcp-server-cisco.html>

```
! --- Mise en place ---
en
conf t
ip dhcp pool LAN-Banc1
dns-server 62.4.16.70
end
wr

! --- Verification ---
```

## Routage définir route par default
```
! --- Mise en place ---
en
conf t
ip route 0.0.0.0 0.0.0.0 214.0.0.2
end
wr

! --- Verification ---
```

## Routage statique ipv4
```
! --- Mise en place ---
en
conf t
!! Route par defaut !!
ip route 0.0.0.0 0.0.0.0 ethernet 1/0
end
wr

! --- Verification ---
!! Verification des interface !!
show ip int brief

!! Verification des route !!
show ip route
```

## Routage dynamique RIPv2
```
! --- Mise en place ---
en
conf t

!! active ripv2 !!
router rip

!! declaration interface LAN passive !!
passive-interface fa0/0

!! declaration des reseaux directement connecter !!
network 192.168.50.0
network 192.168.255.0
network 208.111.123.0
end
wr

! --- Verification ---

!! Verification de la table de routage !!
show ip route

!! Verification de bd de routage RIP !!
show ip rip database
```
##  OSPF Routage dynamique

<https://youtu.be/6FeNi1djwc8>
```
! --- Mise en place ---
en
conf t

!! active ospf !!
router osfp 1

!! declarer les reseaux avec le masque inverser !!
!! area 0 creer de area de router pas plus de 15 par area !!
network 192.168.1.0 0.0.0.255 area 0
network 1.1.1.0 0.0.0.255 area 0
network 4.4.4.0 0.0.0.255 area 0
network 5.5.5.0 0.0.0.255 area 0

!! Declarer le reseau local !!
router ospf
passive-interface fa 0/1

! --- Verification ---
!! Voir les voisins OSPF dans la meme area !!
show ip ospf neighbor

!! voire la db OSPF !!
show ip ospf database

!! voire les routes !!
show ip route

! --- Les coups de laisons ---
!! Pour modifier le coup des interface !!
int fa 0/0
ip ospf cost 10
```


## Nat statique
```
! --- Mise en place ---
! Prerequis configurer ip des interface
! Prerequis configurer route par defaut

en
conf t
!! cree acces list !!
access-list 1 permit 172.16.0.0 0.0.255.255

!! pool ip public !!
ip nat pool INTERNET 214.0.0.2 214.0.0.2 netmask 255.255.255.252

!! lien les 2 pool !!
ip nat inside source list 1 pool INTERNET

!! definir interface inside !!
int fa 0/0
ip nat inside

!! definir interface outside !!
int eth 1/0
ip nat outside
end
wr

! --- Verification ---
!! Verification la NAT actif !!
show ip nat translations
```