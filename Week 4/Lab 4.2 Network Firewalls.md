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
  - create firewall rules on LAN-TO-DMZ that allows:
    - 80/tcp from LAN to web01.
  - set firewall ipv4 name LAN-to-DMZ rule 10 action accept
  - set firewall ipv4 name LAN-to-DMZ rule 10 protocol tcp
  - set firewall ipv4 name LAN-to-DMZ rule 10 destination address 172.16.50.3
  - set firewall ipv4 name LAN-to-DMZ rule 10 destination port 80
    - 22/tcp from mgmt01 to the DMZ
  - set firewall ipv4 name LAN-to-DMZ rule 20 action accept
  - set firewall ipv4 name LAN-to-DMZ rule 20 protocol tcp
  - set firewall ipv4 name LAN-to-DMZ rule 20 source address 172.16.200.11
  - set firewall ipv4 name LAN-to-DMZ rule 20 destination address 172.16.50.0/29
  - set firewall ipv4 name LAN-to-DMZ rule 20 destination port 22

- Step 11: DMZ to LAN Firewall
  - allow DMZ traffic back through
    - set firewall ipv4 name DMZ-to-LAN rule 1 action accept
    - set firewall ipv4 name DMZ-to-LAN rule 1 state established



## Task 3: Configuring fw-mgmt
- Step 1: Create LAN and MGMT zones and assign interfaces
  - set firewall zone LAN
  - set firewall zone MGMT
  - set firewall zone LAN member interface eth0
  - set firewall zone MGMT member interface eth1
  - commit -> save

- Step 2: Create LAN-to-MGMT firewall
- set firewall ipv4 name LAN-to-MGMT default-action drop
- set firewall ipv4 name LAN-to-MGMT default-log

  - Rule 10: Allow Wazuh agent communications (1514-1515/tcp) from LAN to wazuh:
    - set firewall ipv4 name LAN-to-MGMT rule 10 action accept
    - set firewall ipv4 name LAN-to-MGMT rule 10 protocol tcp
    - set firewall ipv4 name LAN-to-MGMT rule 10 destination address 172.16.200.10
    - set firewall ipv4 name LAN-to-MGMT rule 10 destination port 1514-1515

  - Rule 20: Allow HTTPS (443/tcp) from mgmt01 to wazuh:
    - set firewall ipv4 name LAN-to-MGMT rule 20 action accept
    - set firewall ipv4 name LAN-to-MGMT rule 20 protocol tcp
    - set firewall ipv4 name LAN-to-MGMT rule 20 source address 172.16.150.10
    - set firewall ipv4 name LAN-to-MGMT rule 20 destination address 172.16.200.10
    - set firewall ipv4 name LAN-to-MGMT rule 20 destination port 443

  - Rule 30: Allow SSH (22/tcp) from mgmt01 to wazuh:
    - set firewall ipv4 name LAN-to-MGMT rule 30 action accept
    - set firewall ipv4 name LAN-to-MGMT rule 30 protocol tcp
    - set firewall ipv4 name LAN-to-MGMT rule 30 source address 172.16.150.10
    - set firewall ipv4 name LAN-to-MGMT rule 30 destination address 172.16.200.10
    - set firewall ipv4 name LAN-to-MGMT rule 30 destination port 22

- Step 3: Create MGMT-to-LAN firewall
- set firewall ipv4 name MGMT-to-LAN default-action drop
- set firewall ipv4 name MGMT-to-LAN default-log

  - Rule 10: Allow MGMT to initiate any connection to LAN (172.16.150.0/24):
    - set firewall ipv4 name MGMT-to-LAN rule 10 action accept
    - set firewall ipv4 name MGMT-to-LAN rule 10 destination address 172.16.150.0/24

  - Rule 20: Allow MGMT to initiate any connection to DMZ (172.16.50.0/29):
    - set firewall ipv4 name MGMT-to-LAN rule 20 action accept
    - set firewall ipv4 name MGMT-to-LAN rule 20 destination address 172.16.50.0/29

- Step 4: Allow established traffic back through
  - set firewall ipv4 name LAN-to-MGMT rule 1 action accept
  - set firewall ipv4 name LAN-to-MGMT rule 1 state established

- Step 5: Assign firewalls to zones
  - set firewall zone MGMT from LAN firewall name LAN-to-MGMT
  - set firewall zone LAN from MGMT firewall name MGMT-to-LAN
  - commit -> save
