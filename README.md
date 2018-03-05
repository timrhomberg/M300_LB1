# M300_LB1

Für die VM wird die Debian Box verwendet. 

Als erstes wird das Paketverzeichnis aktualisiert. Im nächsten Schritt wird der DHCP Server installiert. Das Paket lautet: ISC-DHCP-SERVER.

Das Konfigurationfile vom DHCP Server befindet sich im Pfad /etc/dhcp/dhcpd.conf.

Im Konfigurationfile werden folgende Paramterer verändert:
Domainname --> labor.local
neuer Scope Bereich

Der Scope Bereich ist folgendermassen konfiguriert:

Subnet --> 192.168.50.0/24

Range --> 192.168.50.50 - 80

Gateway --> 192.168.50.1

Code:
  sudo sed -i 's/example.org/labor.local/g' /etc/dhcp/dhcpd.conf
  sudo sed -i 's/ns2.labor.local/8.8.8.8/g' /etc/dhcp/dhcpd.conf
  sudo sed -i 's/#authoritative/authoritative/g' /etc/dhcp/dhcpd.conf
  sudo sed -i '$asubnet 192.168.50.0 netmask 255.255.255.0 {' /etc/dhcp/dhcpd.conf
  sudo sed -i '$arange 192.168.50.50 192.168.50.80;' /etc/dhcp/dhcpd.conf
  sudo sed -i '$aoption routers 192.168.50.1;' /etc/dhcp/dhcpd.conf
  sudo sed -i '$a}' /etc/dhcp/dhcpd.conf

