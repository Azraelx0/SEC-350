# Network Firewalls
### In this writeup we are going to manage traffic between the LAN, DMZ, WAN and MGMT networks by implementing Firewall functionality.

### To ensure that our network is properly configured for this lab we will confirm the following
- rw01 can ping web01 via its static route, rw01 can browse to web01
- wks01 can browse web01
- wks01 can browse wazuh 
- web01 can ping wazuh

## Task 1: Configuring fw01
- Create and link firewall zones to interfaces (eth0, eth1, eth2)
  - set firewall zone WAN
  - set firewall zone DMZ
  - set firewall zone LAN
  - set firewall zone WAN member interface eth0
  - set firewall zone DMZ member interface eth1
  - set firewall zone LAN member interface eth2
  - commit save

## Task 2: Creating default drop and log rules across fw01
- Step 1: WAN and DMZ firewalls
  - Firewalls for WAN and DMZ

  - Assigning Firewalls to Zones

- Step 2: Allow http inbound

- Step 3: Allowing established connections back out again

- Step 4: DMZ and LAN Traffic Firewalls
  - Firewalls

  - Zones

- Step 5: Wassup with Wazuh?
  - DMZ-to-LAN

- Step 6: Allow Wazuh agent communications

- Step 7: Allowing established Wazuh traffic back

- Step 8: Configure LAN-TO-WAN Firewall

- Step 9: Configure WAN-TO-LAN Firewall
- Step 10: Configure LAN to DMZ Firewall
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
