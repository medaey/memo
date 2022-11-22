### Serveur RDP

```bash
sudo apt update -y && sudo apt upgrade -y
sudo apt install xfce4 xfce4-goodies xorg dbus-x11 x11-xserver-utils -y
sudo apt install xrdp -y
sudo systemctl status xrdp

sudo adduser xrdp ssl-cert
sudo systemctl restart xrdp

sudo apt install -y ufw
sudo ufw allow 22/tcp
sudo ufw allow 3389
```

### Client ICMPv4

```bash
ping
 -c 3		# Envoi 3 packet
 -i 5		# Intervale de 5 seconde
 -M do		# Ne pas fragmenter
 -s 1400	# Taill des data en octet

traceroute
 -n		# Information en numérique
 -T		# Packet TCP SYN
 -I		# Packet ICMP
```