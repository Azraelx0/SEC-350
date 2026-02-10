# Network Firewalls
### In this writeup we are going to manage traffic between the LAN, DMZ, WAN and MGMT networks by implementing Firewall functionality.

### To ensure that our network is properly configured for this lab we will confirm the following
- rw01 can ping web01 via its static route, rw01 can browse to web01
- wks01 can browse web01
- wks01 can browse wazuh 
- web01 can ping wazuh
# NOTE FOR SOME REASON CANNOT BROWSE TO WAZUH DASH FROM WKS01, KEEP IN MIND IN CASE OF TROUBLESHOOTING

## Task 1: Configuring fw01
- Create and link firewall zones to interfaces (eth0, eth1, eth2)
  - set firewall zone WAN
  - set firewall zone DMZ
  - set firewall zone LAN
  - set firewall zone WAN member interface eth0
  - set firewall zone DMZ member interface eth1
  - set firewall zone LAN member interface eth2
  - commit -> save

## Task 2: Creating default drop and log rules across fw01
- Step 1: WAN and DMZ firewalls
  - Firewalls for WAN and DMZ
    - set firewall ipv4 name WAN-to-DMZ default-action drop
    - set firewall ipv4 name DMZ-to-WAN default-action drop
    - set firewall ipv4 name WAN-to-DMZ default-log 
    - set firewall ipv4 name DMZ-to-WAN default-log
  - Assigning Firewalls to Zones
    - set firewall zone WAN from DMZ firewall name DMZ-to-WAN 
    - set firewall zone DMZ from WAN firewall name WAN-to-DMZ 
    - commit -> save

- Step 2: Allow http inbound
  - set firewall ipv4 name WAN-to-DMZ rule 10 action accept
  - set firewall ipv4 name WAN-to-DMZ rule 10 protocol tcp
  - set firewall ipv4 name WAN-to-DMZ rule 10 destination address 172.16.50.3
  - set firewall ipv4 name WAN-to-DMZ rule 10 destination port 80

- Step 3: Allowing established connections back out again
  - set firewall ipv4 name DMZ-to-WAN rule 1 action accept
  - set firewall ipv4 name DMZ-to-WAN rule 1 state established

- Step 4: DMZ and LAN Traffic Firewalls
  - Firewalls
    - set firewall ipv4 name LAN-to-DMZ default-action drop
    - set firewall ipv4 name DMZ-to-LAN default-action drop
    - set firewall ipv4 name LAN-to-DMZ default-log 
    - set firewall ipv4 name DMZ-to-LAN default-log
  - Zones
    - set firewall zone LAN from DMZ firewall name DMZ-to-LAN 
    - set firewall zone DMZ from LAN firewall name LAN-to-DMZ
    - commit -> save

- Step 5: Wassup with Wazuh?
  - Some context: Right now we have firewalls whose only rules are to drop everything (except port 80 to web01 from the WAN).
  - The Wazuh server expects clients to connect to it via tcp/1514 and tcp/1515.

- Step 6: Allow Wazuh agent communications
  - set firewall ipv4 name DMZ-to-LAN rule 10 action accept
  - set firewall ipv4 name DMZ-to-LAN rule 10 description "Wazuh agent communications with Wazuh server"
  - set firewall ipv4 name DMZ-to-LAN rule 10 destination address 172.16.200.10
  - set firewall ipv4 name DMZ-to-LAN rule 10 destination port 1514-1515
  - set firewall ipv4 name DMZ-to-LAN rule 10 protocol tcp

- Step 7: Allowing established Wazuh traffic back
  - set firewall ipv4 name DMZ-to-WAN rule 1 action accept
  - set firewall ipv4 name DMZ-to-WAN rule 1 state established

- Step 8 & 9: Configure LAN-TO-WAN & WAN-TO-LAN Firewall
  - Firewalls
    - set firewall ipv4 name LAN-to-WAN default-action drop
    - set firewall ipv4 name WAN-to-LAN default-action drop
    - set firewall ipv4 name LAN-to-WAN default-log
    - set firewall ipv4 name WAN-to-LAN default-log
  - Zones
    - set firewall zone WAN from LAN firewall name LAN-to-WAN
    - set firewall zone LAN from WAN firewall name WAN-to-LAN
  - Rules
    - set firewall ipv4 name LAN-to-WAN rule 1 action accept 
    - set firewall ipv4 name WAN-to-LAN rule 1 action accept
    - set firewall ipv4 name WAN-to-LAN rule 1 state established

- Step 10: Configure LAN to DMZ Firewall
# I am here
- Step 11: DMZ to LAN Firewall

## Task 3: Configuring fw-mgmt

Create LAN and MGMT zones on fw-mgmt
Create both zones and assign the correct interfaces and firewalls.
LAN-to-MGMT
Create a LAN-to-MGMT firewall that:
Allows 1514,1515/tcp from LAN to wazuh
Allows 443/tcp from mgmt01 on LAN to wazuh
Allows 22/tcp from mgmt01 on LAN to wazuh
Allows established traffic back through the related firewall

MGMT-to-LAN
Create a MGMT-TO-LAN firewall that:
Allows MGMT to initiate any connection to the LAN
Allows MGMT to initiate any connection to the DMZ
Allows established traffic back again
If you do this right, you should be able to connect from mgmt02 to the DMZ like so.
wget to web01
ping to mgmt01
ping outside will fail because you didn't explicitly allow MGMT to go anywhere but LAN and DMZ.
