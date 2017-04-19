# propaganda

This repository contains all code and documentation regarding the automated glass Defacto sign.

## Hardware

```
Raspberry Pi 3
DS1302 real-time clock module
Generic 5V relais module
Generic 5V 3A power supply
```

## Diagram
![Diagram](diagram.png)

## Configuration
### Enable RTC module
 
Compile the RTC program:
`cc rtc-pi.c -o rtc-pi`

Set the time (replace time format by actual date/time):
`sudo ./rtc-pi YYYYMMDDhhmmss`

Test if the RTC contains the right date/time:
`sudo ./rtc-pi`

### Syncing PI clock with module
A RTC module would be useless on the PI without using it to retrieve the right clock at the right time. And the PI needs a clock the most at boot.

The simplest method would be to edit */etc/rc.local* and add a call to the compiled rtc-pi binary.

But to read a valid clock from the module we require it to have the correct time set beforehand. For this we can use the same binary to save the correct time (obtained most likely via NTP) on a previous shutdown.

For this, create a new file (let’s call it *savetime*) in */etc/init.d* with the following content:

```
#!/bin/bash
# saving date/time to RTC module
/usr/sbin/rtc-pi `date +"%Y%m%d%H%M%S"`
```

Then symlink this file in */etc/rc0.d*

`ln -s /etc/init.d/savetime /etc/rc0.d/K01savetime`

*rc0.d* is the folder for scripts called at runlevel 0 (shutdown/restart)
