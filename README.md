# Home Lab simulators using VMware Workstation Pro

## Download the appropriate simulator here:

https://mysupport.netapp.com/site/tools/tool-eula/simulate-ontap

## Deploy the OVA to VMware workstation
![image](https://github.com/DDTully/SimSetup/assets/165563299/e0e9a163-1736-4e9a-b31d-ce43de759f10)
![image](https://github.com/DDTully/SimSetup/assets/165563299/3e134e5c-ecc0-49ad-ba89-f044a25f7cc9)
![image](https://github.com/DDTully/SimSetup/assets/165563299/34c83e05-c466-46d7-bd40-83ec07c9ae0b)

## Correct Virtual Machine Networking
![image](https://github.com/DDTully/SimSetup/assets/165563299/7c6142f0-1a2c-4502-9fa8-eed8d7cf6700)

Set at least 2 Network Adapters to Bridged for management/data, leave 2 as Host-only if using a multi-node cluster.

![image](https://github.com/DDTully/SimSetup/assets/165563299/1018ec25-744d-4e4b-9ce3-b489e58b681a)

Additional Network Adapters can be added at any time, however ONTAP will need to reboot in order to detect them.

## Add a serial port (You’ll thank me later)
![image](https://github.com/DDTully/SimSetup/assets/165563299/a478707d-1a99-4cee-9651-1218beaa118f)

For the serial port settings, select “use named pipe” and use the following settings:
- \\.\pipe\nameofsim-cons (name this to suit your needs)
- This end is the server
- The other end is a virtual machine	
- Uncheck Yield CPU on poll

![image](https://github.com/DDTully/SimSetup/assets/165563299/14402c7c-6833-4ef9-b18d-cbf5edf4fe52)

## Set up PuTTY session and save the config. 
(It won’t connect while the VM is powered off)

![image](https://github.com/DDTully/SimSetup/assets/165563299/522d8b3a-076a-49a5-9560-ea4fef40d7f1)

## Power on the VM, then press the spacebar to get to the VLOADER prompt
![image](https://github.com/DDTully/SimSetup/assets/165563299/addedbaa-8826-4176-98c6-fd295d6ad6e5)

Connect the previously saved PuTTY session, hit enter a few times to ensure functionality.

![image](https://github.com/DDTully/SimSetup/assets/165563299/9e525cfa-d089-4193-b867-3212dd2d3422)

From this point, we don’t need to worry about the VMware video console, and we can copy/paste to PuTTY.

## Set the primary console type to comconsole, then autoboot
```
set console=comconsole,vidconsole
autoboot
```
## Disk Configuration

Set the admin password
```
security login password -username admin
```
Unlock the diag user
```
security login unlock -username diag
```
Set the diag password 
```
security login password -username diag
```
Login to systemshell
```
set -confirmations off
set d
systemshell
```









