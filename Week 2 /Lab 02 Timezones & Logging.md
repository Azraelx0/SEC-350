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

## Log Organization
Remote logs should be segregated and ideally stored on reliable and redundant storage in a manner that supports dealing with discrete event types. We are going to store logs in a directory hierarchy in order to provide this organization.

- On log01
  - go into /etc/rsyslog.conf
  - comment out UDP and TCP syslog reception modules/input lines
- Create custom rsyslog drop-in file
  - cd /etc/rsyslog.d
  - sudo curl https://raw.githubusercontent.com/gmcyber/sec350-share/main/03-sec350.conf
  - cat 03-sec350.conf
### This configuration file (03-sec350.conf) will dynamically create and name files based upon hostname, date and process name. Input over udp 514 is associated with the RemoteDevice ruleset, which in turn uses the dynamic template configuration called “DynFile”.

To test we can go back to web01 and run
 - logger -t I am testing rsyslog from web01 
Then back on log01
  - sudo ls -lR --color /var/log/remote-syslog/
  - sudo cat /var/log/remote-syslog/web01-azrael/2026.01.26.I.log

## Web01: Logging Authorization Events
Modify the rsyslog client configuration on web01 so that authentication events are forwarded to our log server. file entry goes to web01 not log01
- sudo vi /etc/rsyslog.d/sec350.conf
- should have lines: user.notice @172.16.50.5 and authpriv.* @172.16.50.5
We can test this by sshing from rw01 into web01 and purposely enter wrong password
Then we can go back to log01 and read the log file
- must swap to root user for this brief time, sudo su
- cd /var/log/remote-syslog/web01-azrael/
- ls
- cat /2026.01.26.sshd.log
- exit root user

## fw01: Logging Authorization Events
We are going to do some critical things in this section:

### We are going to adjust the VyOS configuration to send authentication messages from fw01 to log01
To do this we use the command:
- set system syslog remote 172.16.50.5 facility authpriv level info

### We will change our vyos user's default password
To change vyos password:
  - set system login user "user" authentication plaintext-password "password here"
  - DONT use '$' sign to avoid errors
If you would like to set an encrypted password can use this command:
  - set system login user vyos authentication encrypted-password <hash>

### We are going to turn on ssh and secure that ssh with rsa key based authentication
First we need to enable the ssh service:
  - set service ssh port 22
Then we need to generate a public key for our user
  - ssh-keygen -t rsa
Now we need to add that key to our user, it's usually stored in ~/.ssh/id_rsa.pub
  - set system login user <username> authentication public-keys <identifier> key "<public-key-contents>"
  - set system login user <username> authentication public-keys <identifier> type ssh-rsa
OR run following command to do it automatically
  - generate public-key-command user vyos path /home/vyos/.ssh/id_rsa.pub
Lastly, best practice would be to disable based password authentication
  - set service ssh disable-password-authentication
