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

- set protocols static route 0.0.0.0/0 next-hop "172.16.150.2"

- Now configure DNS
  - set system name-server "172.16.150.3"
  - set service dns forwarding listen-address "172.16.150.3"
  - set service dns forwarding allow-from "172.16.150.3/28"
  - set service dns forwarding system
  - commit > save > exit
 
### Step 4: Configure mgmt02
- We repeat step 1

### Step 5: RIP on FW1 and FW-MGMT
- On fw01, enable RIP on eth1(LAN) and advertise the DMZ Network
  - set protocols rip interface eth1
  - share routes to the DMZ with below command
  - set protocols rip network 172.16.50.0/29

- On fw-mgmt, Enable RIP on eth0(LAN) and advertise the MGMT network
  - set protocols rip interface eth0
  - share routes to the management network with below command
  - set protocols rip network '172.16.200.0/28'

### Step 6: Shutdown log01
- We will transition log capabilities to wazuh VM

### Step 7: Configure server Wazuh
- configure network access
  - /etc/netplan/50-cloud-init.yaml is the config file
  - change dhcp4 from true -> false
  - added following lines
    addresses:
     - 172.16.200.10/28
    nameservers:
        addresses: [172.16.200.2]
    routes:
     - to: 0.0.0.0/0
       via: 172.16.200.2
  - netplan apply

### Export the firewall configurations for fw-mgmt and fw1
- show configuration commands | grep -v "syslog global\|ntp\|login\|console\|config\|hw-id\|loopback\|conntrack"
fw-mgmt:
<img width="1895" height="898" alt="image" src="https://github.com/user-attachments/assets/98c03717-95e2-45c3-8ea4-25162f5ee4ee" />
fw01:
<img width="1908" height="744" alt="image" src="https://github.com/user-attachments/assets/e046632c-a6e4-4650-b6ae-ee7105883eaf" />
<img width="1394" height="1072" alt="image" src="https://github.com/user-attachments/assets/bb8197cc-0889-4930-aff2-5fffcd4318f1" />

