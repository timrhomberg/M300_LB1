# M300_LB1

Als erstes habe ich die Multi VM Umgebung aufgebaut. Das File und der Code befindet sich <a href="https://github.com/mc-b/devops/tree/master/vagrant">Hier</a>.

Für die VM wird die Debian Box verwendet (<a href="https://app.vagrantup.com/debian/boxes/jessie64">Link</a>). 

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
