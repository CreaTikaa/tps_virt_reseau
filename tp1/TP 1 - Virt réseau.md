# **Part 1 : Most simplest LAN**

## **1. Intro**

**3. Know your MAC**

```bash
[crea@node1 ~]$ ip a
[...]
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:a9:d0:7b brd ff:ff:ff:ff:ff:ff
    altname enx080027a9d07b
    inet 10.1.1.1/24 brd 10.1.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::c25f:5178:213e:b3a8/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```

```bash
[crea@node2 ~]$ ip a
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:08:0a:b0 brd ff:ff:ff:ff:ff:ff
    altname enx080027080ab0
    inet 10.1.1.2/24 brd 10.1.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::7748:4c87:d1db:a0c1/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```

### **4. IP Setup**

Option 1 : 

On lance `nmtui` qui ouvre un petit GUI puis : 

→ Edit a connection

→ Mettre “IPv4 CONFIGURATION” en “Manual” 

→ Add une adresse, puis aller sur “Ok” en bas

→ Puis quitter le menu et lancer 
`nmcli con down enp0s3 && nmcli con up enp0s3`

Option 2 : 

La commande all-in-one

```bash
nmcli con mod enp0s8 ipv4.gateway '' && nmcli con mod enp0s8 ipv4.address '10.1.1.1' && nmcli con mod enp0s8 ipv4.method manual && nmcli con down enp0s8 && nmcli con up enp0s8
```

🌞 **Proof !**

```bash
[crea@node1 ~]$ ip a
[...]
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:a9:d0:7b brd ff:ff:ff:ff:ff:ff
    altname enx080027a9d07b
    inet 10.1.1.1/24 brd 10.1.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::c25f:5178:213e:b3a8/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```

```bash
[crea@node2 ~]$ ip a
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:08:0a:b0 brd ff:ff:ff:ff:ff:ff
    altname enx080027080ab0
    inet 10.1.1.2/24 brd 10.1.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::7748:4c87:d1db:a0c1/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```

Effectuer un `ping` d'une machine à l'autre

```bash
[crea@node1 ~]$ ping 10.1.1.2
PING 10.1.1.2 (10.1.1.2) 56(84) bytes of data.
64 bytes from 10.1.1.2: icmp_seq=1 ttl=64 time=1.13 ms
64 bytes from 10.1.1.2: icmp_seq=2 ttl=64 time=0.785 ms
64 bytes from 10.1.1.2: icmp_seq=3 ttl=64 time=0.743 ms
^C
--- 10.1.1.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2036ms

[crea@node2 ~]$ ping 10.1.1.1                                                                                      
PING 10.1.1.1 (10.1.1.1) 56(84) bytes of data.
64 bytes from 10.1.1.1: icmp_seq=1 ttl=64 time=1.42 ms
64 bytes from 10.1.1.1: icmp_seq=2 ttl=64 time=0.827 ms
64 bytes from 10.1.1.1: icmp_seq=3 ttl=64 time=0.890 ms
^C
--- 10.1.1.1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2006ms
```

**Protocolz ?**

Les pings utilisent le protocole ICMP. 

Voir “capture_ping.pcap”

# **Part 2 : Bring that switch in**

🌞 **Déterminer l'adresse MAC de vos trois machines**

- une commande dans le terminal de chaque machine

🌞 **Définir une IP statique sur les trois machines**

- prouver que votre changement d'IP est effectif sur chaque machine

🌞 **Effectuer des `ping` d'une machine à l'autre**

```bash
[crea@node1 ~]$ ip a
[...]
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:a9:d0:7b brd ff:ff:ff:ff:ff:ff
    altname enx080027a9d07b
    inet 10.1.1.1/24 brd 10.1.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::7748:4c87:d1db:a0c1/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
       
[crea@node1 ~]$ ping 10.1.1.2
PING 10.1.1.2 (10.1.1.2) 56(84) bytes of data.
64 bytes from 10.1.1.2: icmp_seq=1 ttl=64 time=0.865 ms
64 bytes from 10.1.1.2: icmp_seq=2 ttl=64 time=0.895 ms

[crea@node1 ~]$ ping 10.1.1.3
PING 10.1.1.3 (10.1.1.3) 56(84) bytes of data.
64 bytes from 10.1.1.3: icmp_seq=1 ttl=64 time=0.865 ms
64 bytes from 10.1.1.3: icmp_seq=2 ttl=64 time=1.03 ms

```

```bash
[crea@node2 ~]$ ip a
[...]
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:08:0a:b0 brd ff:ff:ff:ff:ff:ff
    altname enx080027080ab0
    inet 10.1.1.2/24 brd 10.1.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::c25f:5178:213e:b3a8/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
      
[crea@node2 ~]$ ping 10.1.1.1
PING 10.1.1.1 (10.1.1.1) 56(84) bytes of data.
64 bytes from 10.1.1.1: icmp_seq=1 ttl=64 time=0.747 ms
64 bytes from 10.1.1.1: icmp_seq=2 ttl=64 time=0.870 ms

[crea@node2 ~]$ ping 10.1.1.3
PING 10.1.1.3 (10.1.1.3) 56(84) bytes of data.
64 bytes from 10.1.1.3: icmp_seq=1 ttl=64 time=1.02 ms
64 bytes from 10.1.1.3: icmp_seq=2 ttl=64 time=1.02 ms
```

```bash
[crea@node3 ~]$ ip a
[...]
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:0f:da:03 brd ff:ff:ff:ff:ff:ff
    altname enx0800270fda03
    inet 10.1.1.3/24 brd 10.1.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::7d69:7fc9:7dce:6fac/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
       
[crea@node3 ~]$ ping 10.1.1.2
PING 10.1.1.2 (10.1.1.2) 56(84) bytes of data.
64 bytes from 10.1.1.2: icmp_seq=1 ttl=64 time=0.610 ms
64 bytes from 10.1.1.2: icmp_seq=2 ttl=64 time=1.03 ms

[crea@node3 ~]$ ping 10.1.1.1
PING 10.1.1.1 (10.1.1.1) 56(84) bytes of data.
64 bytes from 10.1.1.1: icmp_seq=1 ttl=64 time=0.630 ms
64 bytes from 10.1.1.1: icmp_seq=2 ttl=64 time=0.341 ms
```

🌞 **Afficher la table ARP de `node1`**

```bash
VPCS> show

VPCS1 10.1.1.1/24
fe80::250:79ff:fe66:6800/64

VPCS> arp
00:50:79:66:68:01 10.1.1.2 expires in 113 seconds 
00:50:79:66:68:02 10.1.1.3 expires in 116 seconds
```

Voir : CAPTURE ARP 1 & 2

# **Part 3 : DHCP is a nice guy**

➜ **Donner un accès Internet à la machine `dhcp.tp1.efrei`**

- pour ce faire, ajoutez une carte réseau NAT à la machine dans VirtualBox, à la main
- une fois la machine démarrée, prouvez en une commande que vous avez un accès internet

```html

[crea@dhcp ~]$ curl https://www.root-me.org/ | head -n 8
[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml" class="no-js" lang="fr" xml:lang="fr">
<head>
<title>
Bienvenue                                        
[Root Me : plateforme d'apprentissage dédiée au Hacking et à la Sécurité de l'Information]
</title>
```

🌞 **Installer un serveur DHCP**

```html
[crea@dhcp ~]$ sudo dnf install dnsmasq
```

- le serveur DHCP doit démarrer automatiquement quand la machine s'allume
- votre serveur  doit attribuer des IP entre `10.1.1.10` et `10.1.1.50`
    
    DHCP
    

```html
[crea@dhcp ~]$ sudo systemctl enable dnsmasq
Created symlink '/etc/systemd/system/multi-user.target.wants/dnsmasq.service' → '/usr/lib/systemd/system/dnsmasq.service'.

[crea@dhcp ~]$ systemctl status dnsmasq
○ dnsmasq.service - DNS caching server.
     Loaded: loaded (/usr/lib/systemd/system/dnsmasq.service; enabled; preset: disabled)
     Active: inactive (dead)
```

```html
[crea@dhcp ~]$ sudo nano /etc/dnsmasq.conf 

interface=enp0s3 (l'interface utilisé dans gns)
#line 184
dhcp-range=10.1.1.10,10.1.1.50,12h
```

## 4. Proof or you're lying

🌞 **Récupérer une IP automatiquement depuis les 3 nodes**

```html
VPCS> show

NAME   IP/MASK              GATEWAY           MAC                LPORT  RHOST:PORT
VPCS1  10.1.1.3/24         10.1.1.254        00:50:79:66:68:02  10010  127.0.0.1:10011
       fe80::250:79ff:fe66:6802/64

VPCS> dhcp -r
DORA IP 10.1.1.47/24 GW 10.1.1.254

VPCS> show

NAME   IP/MASK              GATEWAY           MAC                LPORT  RHOST:PORT
VPCS1  10.1.1.47/24         10.1.1.254        00:50:79:66:68:02  10010  127.0.0.1:10011
       fe80::250:79ff:fe66:6802/64
```

Voir capture DHCP

## **5. DHCP lease**

```html
[crea@dhcp ~]$ cat  /var/lib/dnsmasq/dnsmasq.leases | grep "10.1.1.45"
1767738262 00:50:79:66:68:00 10.1.1.45 VPCS 01:00:50:79:66:68:00
```

# **Part 4 : real haxor**

🌞 **Installez et configurez un serveur DHCP** sur votre machine attaquante

```html
interface=enp0s3
dhcp-range=10.1.1.210,10.1.1.250,12h
```

🌞 **Test !**

• d'abord, stoppez le service DHCP "légitime" sur la machine `dhcp.tp1.efrei`

```html
○ dnsmasq.service - DNS caching server.
     Loaded: loaded (/usr/lib/systemd/system/dnsmasq.service; enabled; preset: disabled)
     Active: inactive (dead) since Tue 2026-01-06 11:47:13 CET; 4s ago
   Duration: 24min 57.425s
 Invocation: 5635d4b4497a4666954b11b857ba268c
    Process: 830 ExecStart=/usr/sbin/dnsmasq (code=exited, status=0/SUCCESS)
   Main PID: 849 (code=exited, status=0/SUCCESS)
   Mem peak: 2.3M
        CPU: 14ms

Jan 06 11:40:58 dhcp.tp1.efrei dnsmasq-dhcp[849]: DHCPDISCOVER(enp0s3) 10.0.4.15 08:00:27:79:63:62
Jan 06 11:40:58 dhcp.tp1.efrei dnsmasq-dhcp[849]: DHCPOFFER(enp0s3) 10.1.1.50 08:00:27:79:63:62
Jan 06 11:40:58 dhcp.tp1.efrei dnsmasq-dhcp[849]: DHCPDISCOVER(enp0s3) 10.0.4.15 08:00:27:79:63:62
Jan 06 11:40:58 dhcp.tp1.efrei dnsmasq-dhcp[849]: DHCPOFFER(enp0s3) 10.1.1.50 08:00:27:79:63:62
Jan 06 11:40:58 dhcp.tp1.efrei dnsmasq-dhcp[849]: DHCPREQUEST(enp0s3) 10.1.1.50 08:00:27:79:63:62
Jan 06 11:40:58 dhcp.tp1.efrei dnsmasq-dhcp[849]: DHCPACK(enp0s3) 10.1.1.50 08:00:27:79:63:62 rogue
Jan 06 11:47:12 dhcp.tp1.efrei dnsmasq[849]: exiting on receipt of SIGTERM
Jan 06 11:47:12 dhcp.tp1.efrei systemd[1]: Stopping dnsmasq.service - DNS caching server....
Jan 06 11:47:13 dhcp.tp1.efrei systemd[1]: dnsmasq.service: Deactivated successfully.
Jan 06 11:47:13 dhcp.tp1.efrei systemd[1]: Stopped dnsmasq.service - DNS caching server..
```

- vérifier qu'un client récupère bien une adresse IP dans la range configurée avec dnsmasq de la machine attaquante
    
    VPCS
    
- on vérifie d'abord chill que la machine attaquante fait correctement tourner un serveur
    
    DHCP
    

```html
VPCS> dhcp -r
DDORA IP 10.1.1.245/24 GW 10.1.1.50
```

## B. Race !

➜ **Now race !**

- rallumez le serveur  légitime
    
    DHCP
    
- demandez une IP avec un client () et voyez qui répond
    
    VPCS
    
- faites-le plusieurs fois pour voir si c'est consistant

```html
[crea@dhcp ~]$ sudo systemctl status dnsmasq
● dnsmasq.service - DNS caching server.
Loaded: loaded (/usr/lib/systemd/system/dnsmasq.service; enabled; preset: disabled)
Active: active (running) since Tue 2026-01-06 12:16:25 CET; 5s ago
Invocation: 02c306fca1d544ea8db562e63b761cfd
Process: 1694 ExecStart=/usr/sbin/dnsmasq (code=exited, status=0/SUCCESS)
Main PID: 1696 (dnsmasq)
Tasks: 1 (limit: 10646)
Memory: 616K (peak: 1.2M)
CPU: 5ms
CGroup: /system.slice/dnsmasq.service
└─1696 /usr/sbin/dnsmasq

Jan 06 12:16:25 dhcp.tp1.efrei systemd[1]: Starting dnsmasq.service - DNS caching server....
Jan 06 12:16:25 dhcp.tp1.efrei dnsmasq[1696]: started, version 2.90 cachesize 150
Jan 06 12:16:25 dhcp.tp1.efrei dnsmasq[1696]: compile time options: IPv6 GNU-getopt DBus no-UBus i18n IDN2 DHCP DHC>
Jan 06 12:16:25 dhcp.tp1.efrei dnsmasq-dhcp[1696]: DHCP, IP range 10.1.1.10 -- 10.1.1.50, lease time 12h
Jan 06 12:16:25 dhcp.tp1.efrei dnsmasq[1696]: reading /etc/resolv.conf
Jan 06 12:16:25 dhcp.tp1.efrei dnsmasq[1696]: using nameserver 10.0.2.3#53
Jan 06 12:16:25 dhcp.tp1.efrei dnsmasq[1696]: read /etc/hosts - 8 names
Jan 06 12:16:25 dhcp.tp1.efrei systemd[1]: Started dnsmasq.service - DNS caching server..
lines 1-20/20 (END)
```

Node 1 : 

```html
VPCS> dhcp -r
DDORA IP 10.1.1.246/24 GW 10.1.1.50
```

Node 2 : 

```html
VPCS> dhcp -r
DORA IP 10.1.1.245/24 GW 10.1.1.50
```

Node 3 : 

```html
VPCS> dhcp -r
DORA IP 10.1.1.47/24 GW 10.1.1.254
```

Node 4 (nouveau VPCS) : 

```html
VPCS> dhcp -r
DORA IP 10.1.1.248/24 GW 10.1.1.50
```

Node 5 (nouveau VPCS) : 

```html
VPCS> dhcp -r
DORA IP 10.1.1.249/24 GW 10.1.1.50
```

Changements de conf pour augmenter les chances de victoire de 10.1.1.50 : 

```html
dhcp-authoritative (que j'ai décommenter dans le fichier de conf dnsmasq)

[crea@rogue ~]$ ping -f 10.1.1.254 (flood ICMP pour ralentir le serveur DHCP légitime)
```

Voir DHCP RACE.pcap

## **2. BONUS : DHCP starvation**

Extrait des logs de dnsmasq : 

```html
Jan 06 17:37:37 dhcp.tp1.efrei dnsmasq-dhcp[847]: DHCPDISCOVER(enp0s3) de:ad:15:2a:5a:13 no address available
Jan 06 17:37:38 dhcp.tp1.efrei dnsmasq-dhcp[847]: DHCPDISCOVER(enp0s3) de:ad:0d:04:b0:be no address available
Jan 06 17:37:38 dhcp.tp1.efrei dnsmasq-dhcp[847]: DHCPDISCOVER(enp0s3) de:ad:1f:4b:89:96 no address available
Jan 06 17:37:39 dhcp.tp1.efrei dnsmasq-dhcp[847]: DHCPDISCOVER(enp0s3) de:ad:0c:71:d0:9b no address available
Jan 06 17:37:39 dhcp.tp1.efrei dnsmasq-dhcp[847]: DHCPDISCOVER(enp0s3) de:ad:25:3e:63:71 no address available
Jan 06 17:37:40 dhcp.tp1.efrei dnsmasq-dhcp[847]: DHCPDISCOVER(enp0s3) de:ad:07:1b:c0:40 no address available
Jan 06 17:37:40 dhcp.tp1.efrei dnsmasq-dhcp[847]: DHCPDISCOVER(enp0s3) de:ad:17:64:bf:b4 no address available
Jan 06 17:37:40 dhcp.tp1.efrei dnsmasq-dhcp[847]: DHCPDISCOVER(enp0s3) de:ad:26:25:e5:63 no address available
```

Quand on essaye sur une nouvelle machine : 

```html
PC7> dhcp -r
DDD
Can't find dhcp server
```

Pour le tool utilisé, je peux pas en citer un parce que j’avais tenté pleins de trucs en passant des scripts reddit, à des tools dispo sur GitHub donc je sais pas exactement lequel à bien marcher (vu que a chaque fois que je recréais un VPCS il me remettait une ancienne MAC déjà register dans les leases et je faisais pas gaffe)

Trucs dont je me souviens que j’ai try : https://github.com/kamorin/DHCPig, https://github.com/kavishkagihan/DHCP-Starvation-Attack/blob/main/exploit.py, https://github.com/kavishkagihan/DHCP-Starvation-Attack, https://github.com/JakubPruzinec/DHCP_starvation_tool. 

## **3. ARP poisoning**

Node 1 : 

```html
VPCS> arp

00:50:79:66:68:01  10.1.1.2 expires in 112 seconds 
00:50:79:66:68:02  10.1.1.3 expires in 116 seconds 
```

Attaque :

```html
crea@debvm:~$ sudo arping -I enp0s9 -S 10.1.1.3 -s SA:LU:UT:ME:OO:OW -c 5 -U 10.1.1.1
```

Explication des options : arping -i pour Interface -S pour Fausse IP -s pour Fausse MAC -c Nombre -U IP de la victime

Après l’attaque : 

```html
VPCS> arp

00:50:79:66:68:01  10.1.1.2 expires in 86 seconds 
de:ad:be:ef:ca:fe  10.1.1.3 expires in 118 seconds 
```

Voir Capture ARP Spoofing

## **B. MITM**

Sur la machine attaquante : 

```html
crea@debvm:~$ sudo arpspoof -i enp0s9 -t 10.1.1.1 10.1.1.1
crea@debvm:~$ sudo arpspoof -i enp0s9 -t 10.1.1.1 10.1.1.2
```

Si on essaye de ping ça timeout donc il faut ajouter : 

```html
crea@debvm:~$ sudo sysctl -w net.ipv4.ip_forward=1 net.ipv4.ip_forward = 1
```

Sur node1 : 

```html
VPCS> arp

08:00:27:22:9a:94  10.1.1.2 expires in 120 seconds 
```

Sur node2 : 

```html
VPCS> arp

08:00:27:22:9a:94  10.1.1.35 expires in 118 seconds 
08:00:27:22:9a:94  10.1.1.1 expires in 119 seconds 
```

Sur la machine attaquante : 

```html
4: enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:22:9a:94 brd ff:ff:ff:ff:ff:ff
    altname enx080027229a94
    inet 10.1.1.35/24 brd 10.1.1.255 scope global enp0s9
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe22:9a94/64 scope link proto kernel_ll 
       valid_lft forever preferred_lft forever
```

Le node1 et node2 se ping bien pendant que nous sommes au millieu.

Voir p4_mitm.
