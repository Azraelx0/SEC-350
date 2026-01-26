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

![Before and After]("C:\Users\Nordi\Pictures\Screenshots\2026-01-26_15-31-21.png")

