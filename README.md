# M1-Ydays

## Topologie

![topo.png](topo.png)

## Configuration DHCP

```bash
$ yum install dhcp
$ systemctl enable dhcpd
$ vim /etc/dhcpd/dchpd.conf

default-lease-time 600;
max-lease-time 7200;

authoritative;
log-facility local7;

subnet 10.2.20.0 netmask 255.255.255.0 {

}

# VLAN 30 - Users
subnet 10.2.30.0 netmask 255.255.255.0 {
        range 10.2.30.100 10.2.30.150;

        option domain-name "ydaynetwork.lab";
        option domaine-name-servers 10.2.10.11;
}

:wq!

$ systemctl start dhcpd
$ systemctl status dhcpd
```

![dhcp.png](dhcp.png)

## Configuration DNS

```bash
$ yum install bind bind-utils -y
```

## Configuration Serveur Web

### Installer Ngninx  

``` bash
$ sudo yum install epel-release
$ sudo yum install nginx 
$ systemctl enable nginx
$ systemctl start nginx
$ systemctl status nginx
```
![srv_web.png](srv_web.png)

### Installer PHP
`sudo apt install php-fpm`

Activer PHP dans Nginx : 
```
cd /etc/nginx
sudo nano sites-enabled/default
```
trouver la ligne `index index.html index.htm;`  
Ajouter `index.php`  après `index` pour ressembler à ceci:
```
index index.php index.html index.htm;
```

### Héberger son site sur le server

Nginx préinstalle une page index.html qui se trouve dans : `cd /var/www/`  

## Configuration Routeurs OSPF
* R2 :
```bash
$ conf t
$ router ospf 1
$ router-id 2.2.2.2
$ network 10.2.10.0 0.0.0.255 area 0
$ network 10.2.30.0 0.0.0.255 area 0
$ network 2.2.2.0 0.0.0.255 area 0
$ network 3.3.3.0 0.0.0.255 area 0
$ passive-interface fastEthernet 0/0
$ exit
$ exit
$ write
```
* R3:
```bash
$ conf t
$ router ospf 1
$ router-id 3.3.3.3
$ network 10.2.20.0 0.0.0.255 area 0
$ network 3.3.3.0 0.0.0.255 area 0
$ network 4.4.4.4 0.0.0.255 area 0
$ passive-interface fastEthernet 0/0
$ exit
$ exit
$ write
```
* R4 :
```bash
$ conf t
$ router ospf 1
$ router-id 4.4.4.4
$ network 2.2.2.0 0.0.0.255 area 0
$ network 4.4.4.0 0.0.0.255 area 0
$ passive-interface fastEthernet 0/0
$ exit
$ exit
$ write
```
* Vérification de la configuration :
```bash
$ sh ip ospf neighbor
$ sh ip ospf database
sh ip route
```