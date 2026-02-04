# Segmentation
## In this lab, we are going to segment our network by adding a new firewall and a new network device and server

### Step 1: Configure WKS01
- This is simply a windows machine
- Add the LAN adapter in hardware
- we will just add user and change hostname

### Step 2: Update fw01 - LAN Configuration
- Enter configuration mode
  - set nat source rule 30 description "NAT FROM MGMT to WAN"
  - set nat source rule 30 outbound-interface name eth0
  - set nat source rule 30 source address 172.16.200.0/28
  - set nat source rule 30 translation address masquerade
- commit > save > exit

### Step 3: Configure fw-mgmt
- Add descriptions for the two adapters we added
  - set interfaces ethernet "eth0" description azrael-LAN
  - set interfaces ethernet "eth1" description azrael-MGMT
  - commit > save > exit
  - can run show interfaces to verify
- Add IPs for each interface
  - set interfaces ethernet "eth0" address "172.16.150.3/24"
  - set interfaces ethernet "eth1" address "172.16.200.2/28"
  - commit > save > exit
  - run show interfaces to verify
 
- Now lets enable next-hop and DNS forwarding

- set protocols static route 0.0.0.0/0 next-hop "172.16.150.3"

- Now configure DNS
  - set system name-server "172.16.150.3"
  - set service dns forwarding listen-address "172.16.150.3"
  - set service dns forwarding allow-from "172.16.150.3/28"
  - set service dns forwarding system
  - commit > save > exit
 
### Step 4: Configure mgmt02
- We repeat step 1

### Step 5: RIP on FW1 and FW-MGMT
