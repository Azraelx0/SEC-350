# Lab 01 
## Overview and checklist
In this lab, we will be completing the following configurations:

- Rw01: This is the “road warrior” linux laptop. A computer that sits outside our organization's network
  - Add sudo user
  - Configure IP configuration (ip, mask, gateway etc.)
  - Configure IP route to direct certain traffic to the organization’s DMZ

Fw01: This is a vyos router/firewall that connects the SEC-350 (ISP), DMZ, and LAN networks
Add and set adapters in proxmox
Configure hostname
Configure ip address configuration per the 3 interfaces
Set default routing rules
Set DNS forwarding and forwarding rules
Set NAT rules

Web01: This is the organization's Rocky web server in the DMZ
Add user, set password, add to sudo (wheel) group
Set hostname
Set ip configuration (static) including Gateway and DNS
Set firewall rules
Configure as a web server
Configure as rsyslog client

Log01: This is the organization’s Rocky log server (in DMZ for now)
Add user, set password, add to sudo (wheel) group
Set hostname
Set ip configuration (static) including Gateway and DNS
Set firewall rules
Configure as rsyslog server
