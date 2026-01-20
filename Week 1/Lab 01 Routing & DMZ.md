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
### Open rw01 vm
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
### Open fw01 VM
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
