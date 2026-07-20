---
tags:
- EPICS
---

# IP
10.100.2.12

# Model
Raspberry pi 3b

# Function
EPICS 

# IOCs
[[IOCs]]: cryo


# System files
`cat /etc/rc.local`
```
#!/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits.
#
# By default this script does nothing.
# Print the IP address
_IP=$(hostname -I) || true
if [ "$_IP" ]; then
  printf "My IP address is %s\n" "$_IP"
fi
su pi -c 'screen -dm -S ioccryo bash -c "sleep 30; cd /home/pi/EPICS/IOCs/cryo/iocBoot/ioccryo/; ./st.cmd;"'
su pi -c 'screen -dm -S cryomech bash -c "sleep 30; cd /home/pi/EPICS/IOCs/cryomech/iocBoot/ioccryomech/; ./st.cmd;"'
su pi -c 'screen -d -m -S AVC /home/pi/EPICS/IOCs/cryo/python/AVC.sh'
su pi -c 'screen -d -m -S TelAlarms /home/pi/EPICS/IOCs/cryo/python/TelAlarms.sh'
exit 0
```

