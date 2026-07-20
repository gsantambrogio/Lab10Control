---
tags:
- EPICS
---

# IP
10.100.2.6

# Model
Raspberry pi 3b

# Function
EPICS 

# IOCs
[[IOCs]]: lasers, bristol621, waveform_gen


# System files
### USB
 `cat /etc/udev/rules.d/99-usb-serial.rules`
 ```
 UBSYSTEM=="tty", ATTRS{idVendor}=="0403", ATTRS{idProduct}=="6001", SYMLINK+="YAG"
SUBSYSTEM=="tty", ATTRS{idVendor}=="10c4", ATTRS{idProduct}=="ea60", ATTRS{serial}=="6492", SYMLINK+="bristol621"
 ```
###  rc.local
 
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
su pi -c 'screen -dmS lasersIOC bash -c "sleep 28; cd /home/pi/Apps/epics/IOCs/lasers/iocBoot/ioclasersIOC/; ./st.cmd"'
su pi -c 'screen -dmS bristol621IOC bash -c "sleep 30; cd /home/pi/Apps/epics/IOCs/bristol621/iocBoot/iocbristol621/; ./st.cmd"'
su pi -c 'screen -dmS waveformIOC bash -c "sleep 32; cd /home/pi/Apps/epics/IOCs/waveform_gen/iocBoot/iocsdg_waveform/; ./st.cmd"'
exit 0

```
