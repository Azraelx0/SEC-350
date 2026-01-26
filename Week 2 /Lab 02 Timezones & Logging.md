# Lab 02 Timezones & Logging
### Essentially, this lab aims to make the times and logging consistent across our environment so we can create accurate timelines of events
## Configuring RW01
First lets look at the current output of syslog
  - Type "date" in terminal and observe
  - Then "logger -t test timingiseverything"
  - Read the log "sudo cat /var/log/syslog | grep timingiseverything"
  - see screenshot after next step for before and after
To fix we will simply comment out a single line on our RW01 machine
  - In the /etc/rsyslog.conf file comment out the line beginning with $ActionFileDefaultTemplate... (line should be just below Global Directives)
  - Restart rsyslog service
  - We can now test this change by using logger -t test timingiseverything2
  - Then read the log with -> sudo cat /var/log/syslog | grep timingiseverything2
  - Before and After seen below
<img width="768" height="134" alt="image" src="https://github.com/user-attachments/assets/203ed6af-1cfc-4fad-b767-6566d0b504e5" />

## Apply this fix to web01 and log01
Here we simply do the same steps as before
  - note conf files do look a little different, just look for default timestamp format

## MGMT01
Next we're going to setup our management system. This will primarily give us the ability to copy/paste to our internal systems user chrome remote desktop
- change network adapter in hardware option to our LAN
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
### Configuring fw01 for NAT and DNS Forwarding
On our local machine our network is setup but we have no internet connection
We must configure rules to route our internet traffic from our LAN to our WAN through our router
- Open fw01
- Enter configuration mode
  - set nat source rule 11 description "NAT FROM LAN to WAN"
  - set nat source rule 11 outbound-interface name eth0
  - set nat source rule 11 source address 172.16.150.0/24
  - set nat source rule 11 translation address masquerade
  - commit > save > exit
Now on our mgmt machine we should be able to ping by IP
  - test by trying to ping 8.8.8.8
  - we get a successful ping
Next we want to enable DNS
- Back on fw01
  - enter config mode
  - set service dns forwarding listen-address "172.16.150.2"
  - set service dns forwarding allow-from "172.16.150.0/24"
  - set service dns forwarding system
  - commit > save > exit
On mgmt machine we can test by pinging a hostname like google.com
We should also be able to navigate to websites from browser

