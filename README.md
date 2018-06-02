# Raspberry Pi Hotspot
there is few line of code for creating hotspot in raspberry pi.

sudo apt-get update
sudo apt-get upgrade
sudo apt-get install dnsmasq hostapd
sudo systemctl stop dnsmasq
sudo systemctl stop hostapd

now we edit this file

sudo nano /etc/dhcpcd.conf

enter following 2 line in this file
interface wlan0
    static ip_address=192.168.43.1/24
in above change ip as you need

sudo service dhcpcd restart

 sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.orig 

edit this file
sudo nano /etc/dnsmasq.conf
  interface=wlan0      # Use the require wireless interface - usually wlan0
  dhcp-range=192.168.43.2,192.168.43.20,255.255.255.0,24h
this is for hotspot provide ip between them

Edit this file
sudo nano /etc/hostapd/hostapd.conf
enter the folllowing line
  interface=wlan0
  driver=nl80211
  ssid=BlackQR   
  hw_mode=g
  channel=7
  wmm_enabled=0
  macaddr_acl=0
  auth_algs=1
  ignore_broadcast_ssid=0
  wpa=2
  wpa_passphrase=123456789
  wpa_key_mgmt=WPA-PSK
  wpa_pairwise=TKIP
  rsn_pairwise=CCMP
  
save it

change in this file
sudo nano /etc/default/hostapd
DAEMON_CONF="/etc/hostapd/hostapd.conf"

sudo systemctl start hostapd
sudo systemctl start dnsmasq

Edit /etc/sysctl.conf and uncomment this line
net.ipv4.ip_forward=1

Add a masquerade for outbound traffic 
sudo iptables -t nat -A  POSTROUTING -o eth0 -j MASQUERADE

Save the iptables rule.
sudo sh -c "iptables-save > /etc/iptables.ipv4.nat"

Edit /etc/rc.local and add this just above "exit 0" to install these rules on boot.
iptables-restore < /etc/iptables.ipv4.nat

Reboot
