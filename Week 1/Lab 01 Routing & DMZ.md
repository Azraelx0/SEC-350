# Lab 01 
## Overview and checklist
In this lab, we will be completing the following configurations:

- Rw01: This is the “road warrior” linux laptop. A computer that sits outside our organization's network
  - Add sudo user
  - Configure IP configuration (ip, mask, gateway etc.)
  - Configure IP route to direct certain traffic to the organization’s DMZ

- Fw01: This is a vyos router/firewall that connects the SEC-350 (ISP), DMZ, and LAN networks
  - Add and set adapters in proxmox
  - Configure hostname
  - Configure ip address configuration per the 3 interfaces
  - Set default routing rules
  - Set DNS forwarding and forwarding rules
  - Set NAT rules

- Web01: This is the organization's Rocky web server in the DMZ
  - Add user, set password, add to sudo (wheel) group
  - Set hostname
  - Set ip configuration (static) including Gateway and DNS
  - Set firewall rules
  - Configure as a web server
  - Configure as rsyslog client

- Log01: This is the organization’s Rocky log server (in DMZ for now)
  - Add user, set password, add to sudo (wheel) group
  - Set hostname
  - Set ip configuration (static) including Gateway and DNS
  - Set firewall rules
  - Configure as rsyslog server

## Configuring rw01
### Open rw01 VM (xubuntu)
- Add a new user
  - sudo adduser azrael
- Add our new user to sudoers group
  - sudo usermod -aG sudo azrael
- Change hostname
  - sudo hostnamectl set-hostname rw01-azrael
- Set static IP address
  - Open network settings
  - select the IPv4 tab
  - click add
  - set address "IP address"
  - set netmask "subnet mask"
  - set gateway "gateway address"
  - set DNS server(s)
  - click save
- Test connection with web browser if desired

## Configuring basic routing on fw01
### Open fw01 VM (VyOS)
- With VyOS remember: configure > commit > save > exit
- Select the hardware tab on vm in proxmoxInterface Assignment
  - add the WAN, LAN, and DMZ network adapters
- Set hostname
  - enter VyOS terminal
  - type "configure" press enter
  - type set system host-name fw01-azrael and press enter
  - type commit and press enter
  - type save and press enter
  - type exit and press enter
### Interface assignment
- We can check active interfaces with the "show interfaces" command
- Run delete command on interfaces if necessary
  - In our case delete any dhcp interfaces if there are any > delete interfaces "adapter (i.e. eth0)" address dhcp
    - commit > save
- Add descriptions for the three adapters we added
  - set interfaces ethernet "adapter" description SEC350-02-WAN
  - set interfaces ethernet "adapter" description azrael-LAN
  - set interfaces ethernet "adapter" description azrael-DMZ
  - commit > save > exit
  - can run show interfaces to verify
- Add IPs for each interface
  - set interfaces ethernet "adapter" address "IPADDRESS/MASK"
  - commit > save > exit
  - run show interfaces to verify
### Gateway and DNS
- We now must tell our VyoS router how to get out to the internet
- enter config mode
  - set protocols static route 0.0.0.0/0 next-hop "gateway"
    - above command sets the next hop so our router knows the path to take
  - Now configure DNS
    - set system name-server "preferred DNS server" (we used our gateway)
  - commit > save > exit

## Configuring web01
### Open web01 VM (rocky web server)
- In proxmox go to hardware tab and select DMZ network adapter
- set IP
  - sudo nmcli connection modify "adapter" ipv4.method manual
  - sudo nmcli connection modify "adapter" ipv4.addresses "172.16.50.3/29"
  - sudo nmcli connection modify "adapter" ipv4.gateway "172.16.50.2"
  - sudo nmcli connection modify "adapter" ipv4.dns "172.16.50.2"
  - sudo nmcli connection down "adapter"
  - sudo nmcli connection up "adapter"
  - test by pinging DMZ interface (on fw01)
- Now add new sudo user
  - sudo useradd -m -G wheel azrael
  - sudo passwd azrael
- Change hostname
  - sudo hostnamectl set-hostname web01-azrael

## Configuring fw01 for NAT and DNS Forwarding on fw01.
### Switch back to fw01 VM
- enter config mode
  - set nat source rule 10 description "NAT FROM DMZ to WAN"
  - set nat source rule 10 outbound-interface name eth0
  - set nat source rule 10 source address 172.16.50.0/29
  - set nat source rule 10 translation address masquerade
  - commit > save > exit
- Test by pinging an IP from web01, pinging by hostname won't work

## Configuring fw01 for DNS forwarding
- enter config mode
  - set service dns forwarding listen-address "DMZ interface"
  - set service dns forwarding allow-from "DMZ gateway/mask"
  - set service dns forwarding system
  - commit > save > exit
- Test by pinging hostname from web01

## Configuring log01
### Open log01 VM
- Configure log01 with an IP address ending in .5.
- Add log01 to DMZ network (change network adapter in hardware tab)
- set IP
  - sudo nmcli connection modify "adapter" ipv4.method manual
  - sudo nmcli connection modify "adapter" ipv4.addresses "IP/MASK"
  - sudo nmcli connection modify "adapter" ipv4.gateway "gateway"
  - sudo nmcli connection modify "adapter" ipv4.dns "DNS server"
  - sudo nmcli connection down "adapter"
  - sudo nmcli connection up "adapter"
- Test by pinging google.com

## Configuring httpd on web01
- See if it's already installed
  - sudo systemctl status httpd
  - If it's not
    - sudo dnf -y install httpd
  - start it
    - sudo systemctl start httpd
    - verify with sudo systemctl status httpd

## Configuring firewall on web01
- sudo firewall-cmd --permanent --add-port=80/tcp
- sudo firewall-cmd --permanent --add-port=443/tcp
- sudo firewall-cmd --reload

## Testing httpd on web01 from rw01
### We need to tell rw01 that any address in our DMZ should route via our firewall’s (VyOS) WAN interface.
### Open rw01 VM
- Open network connections
- select wired connection 1
  - IPv4 Settings
  - Select routes box
  - Select add
  - for address add the DMZ network
  - netmask in our case is 255.255.255.248 or /29
  - gateway is our WAN interface
- restart network using sudo systemctl restart NetworkManager
- Test access to our web01 web server by typing its ip into browser on rw01 VM

## Configuring rsyslog services on log01
- Check that rsyslog is install using sudo systemctl status rsyslog
- Allow port 514 TCP/UDP through firewall
  - sudo firewall-cmd --permanent --add-port=514/tcp
  - sudo firewall-cmd --permanent --add-port=514/udp
  - sudo firewall-cmd --reload
- We also edit the /etc/rsyslog.conf file to allow TCP and UDP modules to be loaded (look for module and input on first line and uncomment)
  - restart rsyslog service > sudo systemctl restart rsyslog
- verify rsyslog is listening on the ports
  - can do this with netstat -tupan : grep 514
    - note: may need to install net-tools to use netstat command

## Configuring rsyslog client on web01
- verify rsyslog is installed and running on web01
  - sudo systemctl status rsyslog
- We create file /etc/rsyslog.d/sec350.conf
- Restart rsyslog
- In this file we add the line "user.notice @<log01-IP>"

## Test rsyslog messaging from web01 to log01
- On our log01 VM we use sudo tail -f the /var/log/messages
- On web01 we use the local logger utility to send a syslog message
  - sudo systemctl restart rsyslog
  - logger -t test TESTFROMWEB01TOLOG01
- On log01 you should see this message

## rw01->SSH->web01->SSH->log01
### From rw01, use a SSH session to login to web01, from that SSH session login to log01.
- In terminal on rw01 type "ssh@web01-IP"
- Once logged into web01 from command line type "ssh@log01-IP"
# Network diagram
# This is the end of this setup writeup
