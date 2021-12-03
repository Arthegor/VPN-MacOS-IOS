# VPN-MacOS-IOS

English version below

# Français
Ceci est une simple configuration IKEv2 permettant une campatibilité maximale avec MacOS/IOS. Le système utilisé est Ubuntu Server 20.04 mais avec quelque modification ( surtout au niveau des emplacement de fichier ainsi que des option de compilation ), ce dernier est compatible avec les distros EL. 

# Installation 

( Toutes les commandes sont tapé en root pour plus de facilité ^^ )

1) Compiler et installer Strongswan

wget https://download.strongswan.org/strongswan-XXX.tar.gz #Remplacer XXX avec la dernière résivison de strongswan 

tar xvf strongswan-XXX.tar.gz #Remplacer XXX avec la dernière résivison de strongswan 

apt-get install build-essential libgmp3-dev

cd strongswan-XXX #Remplacer XXX avec la dernière résivison de strongswan 

./configure --enable-eap-identity --enable-eap-md5 --enable-eap-mschapv2 --enable-eap-tls --enable-eap-ttls --enable-eap-peap --enable-eap-tnc --enable-eap-dynamic --enable-eap-radius --enable-md4 --with-systemdsystemunitdir=/lib/systemd/system

make

make install

2) Créer le certificat wildcard Let's Encrypt 

#Pour cette partie nous présumons que vous possédez un nom de domaine valide chez un fournisseur de nom de domaine

apt install certbot 

certbot certonly --manual --preferred-challenges dns -d "*.DOMAIN" #Remplacer DOMAIN avec votre propre nom de domaine sans supprimer *. qui se trouve avant

#Suivez la procédure de certbot, pour l'enregistrement DNS veuillez voir avec votre fournisseur de nom de domaine pour connaitre la marche à suivre. 

3) Copier les fihiers

#Copier le certificat 

cp /etc/letsencrypt/live/XXX/fullchain.pem /usr/local/etc/ipsec.d/certs
cp /etc/letsencrypt/live/XXX/chain.pem /usr/local/etc/ipsec.d/cacerts
cp /etc/letsencrypt/live/XXX/privkey.pem /usr/local/etc/ipsec.d/private
 
 #Copier les fichiers de configuration

cp ipsec.conf /usr/local/etc/
cp ipsec.secrets /usr/local/etc/

4) Modifier les fichiers ipsec.conf et ipsec.secrets avec vos propre paramètres ( voir les commentaires présents dans les 2 fichiers )

5) Configure NAT 

iptables -t nat -A POSTROUTING -s 10.79.1.0/24 -o ens160 -j MASQUERADE #changer l'adresse IP avec celle spécifié dans ipsec.conf

iptables -t nat -A POSTROUTING -o ens160 -j MASQUERADE

iptables -I FORWARD 1 -j ACCEPT

sysctl -w net.ipv4.ip_forward=1

sysctl -w net.ipv4.conf.all.forwarding=1

6) restart and enable Strongswan

systemctl restart strongswan-starter.service

systemctl enable strongswan-starter.service

7) Profitez 

#N'oubliez pas d'ouvrir les port 500/udp et 4500/udp sur votre routeur ou firewall ( renseignez vous auprès de votre opérateur ou du fabicant du firewall pour connaitre la marche a suivre 

# English
This is simple IKEv2 VPN system designed with best compatibility with MacOS/IOS native VPN tool. The used system is Ubuntu Server 20.04 but it's compatible with some modification ( path and compile option ) with EL distro's. 

# Installation 

( All command is intended with root privilège for facility ^^ )

1) Compile and install Strongswam

wget https://download.strongswan.org/strongswan-XXX.tar.gz #Remplace XXX with latest revision of strongswan

tar xvf strongswan-XXX.tar.gz #Remplace XXX with latest révision of strongswan

apt-get install build-essential libgmp3-dev

cd strongswan-XXX #Remplace XXX with latest révision of strongswan

./configure --enable-eap-identity --enable-eap-md5 --enable-eap-mschapv2 --enable-eap-tls --enable-eap-ttls --enable-eap-peap --enable-eap-tnc --enable-eap-dynamic --enable-eap-radius --enable-md4 --with-systemdsystemunitdir=/lib/systemd/system

make

make install

2) Create Let's encrypt Wildcard Certificate 

#For this part we assume you have a valid domain name register on domain name provider 

apt install certbot 

certbot certonly --manual --preferred-challenges dns -d "*.DOMAIN" #Remplace DOMAIN with your own domain withopur delete *. before your domain

#Follow the guided setup, for the TXT record check with your domain name provider for the procedure.

3) Copy files

#Copy certificate 

cp /etc/letsencrypt/live/XXX/fullchain.pem /usr/local/etc/ipsec.d/certs
cp /etc/letsencrypt/live/XXX/chain.pem /usr/local/etc/ipsec.d/cacerts
cp /etc/letsencrypt/live/XXX/privkey.pem /usr/local/etc/ipsec.d/private
 
 #Copy file configuration

cp ipsec.conf /usr/local/etc/
cp ipsec.secrets /usr/local/etc/

4) Modify ipsec.conf and ipsec.secrets with your own parameter ( check comment in file )

5) Configure NAT 

iptables -t nat -A POSTROUTING -s 10.79.1.0/24 -o ens160 -j MASQUERADE #Change ip with your ip subnet configured in ipsec.conf

iptables -t nat -A POSTROUTING -o ens160 -j MASQUERADE

iptables -I FORWARD 1 -j ACCEPT

sysctl -w net.ipv4.ip_forward=1

sysctl -w net.ipv4.conf.all.forwarding=1

6) restart and enable Strongswan

systemctl restart strongswan-starter.service

systemctl enable strongswan-starter.service

7) Enjoy 

#Dont Forget to open port 500/udp and 4500/udp in your router / firewall ( check your isp or firewall guide for procedure ) 
