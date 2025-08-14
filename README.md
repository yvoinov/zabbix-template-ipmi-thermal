# Template for getting sensor values directly from ipmitool
[![License](https://img.shields.io/badge/License-MIT--Clause-blue.svg)](https://github.com/yvoinov/zabbix-template-ipmi-thermal/blob/main/LICENSE)
## Motivation

The motivation for the development of this template was simple. You do not have a separate independent system of thermal monitoring in the server, both to control global overheating and individual zones and you need to control temperature and prevention in case of failure of the air conditioning system.

Unfortunately, the current implementation of the IPMI-agent to the Zabbix does not allow continuous reading of IPMI sensors, especially for old systems. This can lead to unreliable temperature monitoring, which, in turn, can lead to the failure of the systems due to the thermal blow.

The template works with any IPMI-based systems using ipmitool.

Note: Although the templates were developed for Zabbix 7.0, they will work on earlier versions as well. Just adjust the version in the template.

## Using template

### Linux

Add /usr/bin/ipmitool to sudoers. For example, /etc/sudoers.d/zabbix file should comtains:
```sh
zabbix ALL=(ALL) NOPASSWD:/usr/sbin/smartctl,/usr/bin/ipmitool
```
Put Temperature_monitoring.conf to /etc/zabbix/zabbix_agent2.d (or /etc/zabbix/zabbix_agent.d) and restart agent.

### Solaris

For template require to add UserParameter in agent config:
```sh
# System temperature monitoring
UserParameter=ipmi.temperature,/usr/sbin/ipmitool sensor | /bin/grep 'Temp 2' | /bin/cut -d"|" -f2 | /bin/tr -d [:blank:]
```
and restart agent.

Solaris does not require root permissions to execute ipmitool from agent.

**Note:** On some Fujitsu servers ambient sensor named "Ambient". For such servers UserParameter must be (on Linux):
```sh
# System temperature monitoring
UserParameter=ipmi.temperature,sudo /usr/bin/ipmitool sensor reading Ambient | /bin/cut -d"|" -f2 | /bin/tr -d [:blank:]
```
or (on Solaris):
```sh
# System temperature monitoring
UserParameter=ipmi.temperature,/usr/sbin/ipmitool sensor reading Ambient | /bin/cut -d"|" -f2 | /bin/tr -d [:blank:]
```