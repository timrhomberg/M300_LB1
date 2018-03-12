# M300_LB1

Vagrantfile
-------------
Als erstes habe ich die Multi VM Umgebung aufgebaut. Das File und der Code befindet sich <a href="https://github.com/mc-b/devops/tree/master/vagrant/mmdb">Hier</a>.

Für die VM wird die Debian Box verwendet (<a href="https://app.vagrantup.com/debian/boxes/jessie64">Link</a>). 

Die VM hat folgende Spezifikationen:
* IP: 192.168.50.4
* Hostname: dhcp
* RAM: 1024 MB
* VM Box: Debian

```
config.vm.define "dhcp" do |dhcp|
    dhcp.vm.box = "debian/jessie64"
    dhcp.vm.hostname = "dhcp"
    dhcp.vm.network "private_network", ip:"192.168.50.4" 
	dhcp.vm.provider "virtualbox" do |vb|
	  vb.memory = "1024"  
	end     
```

Als erstes wird das Paketverzeichnis aktualisiert. Im nächsten Schritt wird der DHCP Server installiert. Das Paket lautet: ISC-DHCP-SERVER.
```
sudo apt-get update
sudo apt-get -y install isc-dhcp-server
```

Das Konfigurationfile vom DHCP Server befindet sich im Pfad /etc/dhcp/dhcpd.conf. Im Konfigurationfile wird folgendes geändert:
* Domainname
* DNS
* DHCP Scope

Der Domainname lautet labor.local
```
sudo sed -i 's/example.org/labor.local/g' /etc/dhcp/dhcpd.conf
```

Der DNS wurde auf 8.8.8.8 (Google DNS) konfiguriert.
```
sudo sed -i 's/ns2.labor.local/8.8.8.8/g' /etc/dhcp/dhcpd.conf
```
Der Scope Bereich hat folgende Parameter:
* Subnet --> 192.168.50.0/24
* Range --> 192.168.50.50 - 80
* Gateway --> 192.168.50.1

```
sudo sed -i 's/example.org/labor.local/g' /etc/dhcp/dhcpd.conf
sudo sed -i 's/ns2.labor.local/8.8.8.8/g' /etc/dhcp/dhcpd.conf
sudo sed -i 's/#authoritative/authoritative/g' /etc/dhcp/dhcpd.conf
sudo sed -i '$asubnet 192.168.50.0 netmask 255.255.255.0 {' /etc/dhcp/dhcpd.conf
sudo sed -i '$arange 192.168.50.50 192.168.50.80;' /etc/dhcp/dhcpd.conf
sudo sed -i '$aoption routers 192.168.50.1;' /etc/dhcp/dhcpd.conf
sudo sed -i '$a}' /etc/dhcp/dhcpd.conf
```
Nach der Konfiguration wird der DHCP Service neu gestartet.
```
sudo service isc-dhcp-server restart
```
Am ende wird noch das Tastaturlayout auf Deutsch Schweiz gestellt.
```
sudo sed -i 's/XKBLAYOUT="us"/XKBLAYOUT="ch"/g' /etc/default/locale
```
Danach wird noch eine lokale Firewall installiert und anschliessend auch konfiguriert. Wir öffnen den Port 22 um via SSH darauf zuzugreifen.
```
sudo apt-get -y install ufw gufw 
sudo ufw allow from 10.0.2.2 to any port 22
sudo ufw --force enable    
```

Aufsetzten
----------
Mit folgendem Befehl kann die VM aufgesetzt werden.
```
vagrant up
```

Um sicherzustellen das der DHCP Server lauft kann eine zusätzlich VM aufgesetzt werden und im gleichen Netz platziert werden. Danach sollte die VM eine IP aus dem Range bekommen.

SSH Keys
---------
RSA Private Key:
```
MIIEogIBAAKCAQEAtMk1DACc0uYjtx8wLT2Dii/WF3h/3NhQD23BjDZYdOcmhkPL
Gmy8dDZNwU6X4/3BvjwbZkSH4A1/dvKermAPVGHtgqDl1e0G1CTc3iB7ga9i5HCR
wmq2729JQ72cfYTYc7k17TNoOdfkYMibV8Kl/TunnJtFt8x9eglblWG7EhZ7TvxF
Rmqg+deKp8FzGDflaK0kTmsHRG1x10I8KfKKKLYdpdJ18UGGjwEoLo53o4EevE5G
vxa6ckw/w6ezNsuBC4PNtb56aH5mSdz5HUL5Bfg9FNMO5z1Kg7SvfHr9bh1OOBpm
EAdoBMEbcnUakySGXwSNwO/KnXjMqOCVf4Z11QIDAQABAoIBAAqN3Izw1DbzvI4K
QhPCDZXZqRQBsuU/s5zS+YOoAI4CmJsqBgdq5a2bJfrtDaz/uXnTpH3Z7lzELPbS
vzTK4to4RVdk8UYF6mokJMjK+KrfhFR1xeylsjxUMODFhwdE5CYNX/qTD7igw/Jq
g7ch4/LesrBP2Egcpg6j0TbtV7B8il8Kfvp2Clt1w2LLFKEK99A3zWLC21xK8ZvT
j6U5xP9LTV+srv/4yGY6MTBG2AzGs9Au7+dCb7ZrsFE5Bk2dO2Gvl3sdk02oJgUC
3sjBnE49yaAqZtjLP2P52Iw57fOYGAQ8vZqatKGDXaES66cet+tFtNHhH+iqP07r
JI+URQECgYEA37S7kRX28iqXvvcjW0YblmMUjFqo73sz9Qk17C8eCNwtZMsyp2+4
+aO1opuCcHn6CER+P4I39RshqVpFJkaP75IM3ev4GbfEMwsgww08hViD6lfqN4CY
dGmbpdCPb5/xAOs/UshpTgmqnu8FOe7QoJ4K+WA3GoNtgTHEBrnxIzUCgYEAzuJT
CTxLNs4rqncLD+rCuZkXwA+TphhK5BA2GZiCubNJ6k1wY4n6ipOaP2uzjOACbnaO
2f44GTBl9G1PT9+JQHOC5wrgCxib+m1hF7XNo/82gbZZuAG8gqZCfMDDqc5NyIcE
b8tG5rZwEfZaSJ5H6vk0dGwZlrCVIecW+Nf+vCECgYA8YHMfPWZhBc3e5KTORaW8
eRFasD1YJVBomgvLqwvYKFS4F3+cYTLzbZPgR0h1QvaQtKu+SE8CAEidhJeVNQY0
Cp8eZkmX51k0zZQSEMh81N8FqKS2RibfhIFVx2xvHCPXs6ZrmVuSjFlYe/pVIHd+
YilkFOvKZB5x+BSIHDdQ4QKBgCH9HuVCiZzcbGIaIrAfwpQZacR9CqXcEdm8LBcy
bi+yG++pf1BrJ8VCkLHgsOPxHZUmVzvLP04sHGP23XPi5rq2/4eTytEn3uBavfvW
O4247SyMV9saNe1FAWFbjgnEwhSy0fDH9cMLsAfTcGvDzU72WD7UT7PpGOcz/xss
6UXhAoGANAkMXjwoTo9cF7aUowBPqPJmjgYHHL1WUBLF8PMc9K/4lIOIV+u/5nrL
bHTDLUzbhtzXHHK7ewzQPuv45I3qTTgcgj+1cvpOVKFJtXOnLfjObwhI6MM8zgKT
lm28PJTNllsESwPMD14oBGemBLoReSRF8WTfFlCH/3ji02z5aZ0=
```
RSA Public Key:
```
AAAAB3NzaC1yc2EAAAADAQABAAABAQC0yTUMAJzS5iO3HzAtPYOKL9YXeH/c2FAPb
cGMNlh05yaGQ8sabLx0Nk3BTpfj/cG+PBtmRIfgDX928p6uYA9UYe2CoOXV7QbUJN
zeIHuBr2LkcJHCarbvb0lDvZx9hNhzuTXtM2g51+RgyJtXwqX9O6ecm0W3zH16CVu
VYbsSFntO/EVGaqD514qnwXMYN+VorSROawdEbXHXQjwp8oooth2l0nXxQYaPASgu
jnejgR68Tka/FrpyTD/Dp7M2y4ELg821vnpofmZJ3PkdQvkF+D0U0w7nPUqDtK98e
v1uHU44GmYQB2gEwRtydRqTJIZfBI3A78qdeMyo4JV/hnXV
````````
