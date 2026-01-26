# Lab 02 Timezones & Logging
### Essentially, this lab aims to make the times and logging consistent across our environment so we can create accurate timelines of events
## Configuring RW01
First lets look at the current output of syslog
  - Type "date" in terminal and observe
  - Then "logger -t test timingiseverything"
  - Read the log "sudo cat /var/log/syslog | grep timingiseverything"
  - Output of this is below screenshot
<img width="751" height="53" alt="image" src="https://github.com/user-attachments/assets/8fe20ab5-c7e2-4a44-afa5-3b94934bc7a5" />
To fix we will simply comment out a single line on our RW01 machine
  - In the /etc/rsyslog.conf file comment out the line beginning with $ActionFileDefaultTemplate... (line should be just below Global Directives)
  - Restart rsyslog service
We can now test this change by using logger -t test timingiseverything2
Then read the log with -> sudo cat /var/log/syslog | grep timingiseverything
- See below screenshot for difference
<img width="751" height="68" alt="image" src="https://github.com/user-attachments/assets/3812c134-ee86-4704-91cf-387a0c08cef7" />

