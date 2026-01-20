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
  - sudo nmcli connection modify "adapter" ipv4.addresses "IP/MASK"
  - sudo nmcli connection modify "adapter" ipv4.gateway "gatewat"
  - sudo nmcli connection modify "adapter" ipv4.dns "DNS server"
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
- 
