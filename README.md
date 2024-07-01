# Home Lab simulators using VMware Workstation Pro

## Download the appropriate simulator here:

https://mysupport.netapp.com/site/tools/tool-eula/simulate-ontap

## Deploy the OVA to VMware workstation
![image](https://github.com/DDTully/SimSetup/assets/165563299/e0e9a163-1736-4e9a-b31d-ce43de759f10)
![image](https://github.com/DDTully/SimSetup/assets/165563299/3e134e5c-ecc0-49ad-ba89-f044a25f7cc9)
![image](https://github.com/DDTully/SimSetup/assets/165563299/34c83e05-c466-46d7-bd40-83ec07c9ae0b)

## Adjust Virtual Machine Networking
![image](https://github.com/DDTully/SimSetup/assets/165563299/7c6142f0-1a2c-4502-9fa8-eed8d7cf6700)

Set at least 2 Network Adapters to Bridged for management/data, leave 2 as Host-only if using a multi-node cluster.

![image](https://github.com/DDTully/SimSetup/assets/165563299/1018ec25-744d-4e4b-9ce3-b489e58b681a)

Additional Network Adapters can be added at any time, however ONTAP will need to reboot in order to detect them.

## Configure Serial Console (You’ll thank me later)
![image](https://github.com/DDTully/SimSetup/assets/165563299/a478707d-1a99-4cee-9651-1218beaa118f)

For the serial port settings, select “use named pipe” and use the following settings:
- \\.\pipe\nameofsim-cons (name this to suit your needs)
- This end is the server
- The other end is a virtual machine	
- Uncheck Yield CPU on poll

![image](https://github.com/DDTully/SimSetup/assets/165563299/14402c7c-6833-4ef9-b18d-cbf5edf4fe52)

Set up PuTTY session and save the config. 
(It won’t connect while the VM is powered off)

![image](https://github.com/DDTully/SimSetup/assets/165563299/522d8b3a-076a-49a5-9560-ea4fef40d7f1)

Power on the VM, then press the spacebar to get to the VLOADER prompt

![image](https://github.com/DDTully/SimSetup/assets/165563299/addedbaa-8826-4176-98c6-fd295d6ad6e5)

Connect the previously saved PuTTY session, hit enter a few times to ensure functionality.

![image](https://github.com/DDTully/SimSetup/assets/165563299/9e525cfa-d089-4193-b867-3212dd2d3422)

From this point, we don’t need to worry about the VMware video console, and we can copy/paste to PuTTY.

Set the primary console type to comconsole, then autoboot
```
set console=comconsole,vidconsole
autoboot
```

## Disk Configuration

Login as admin and set the admin password
```
security login password -username admin
```
Unlock the diag user and set the diag password
```
security login unlock -username diag
security login password -username diag
```
Login to systemshell
```
set -confirmations off
set d
systemshell
```
Remove all existing drives and reservations
```
cd /sim/dev/,disks
sudo rm v*
sudo rm ,reservations
cd /sim/dev
```
Create 20, 9000MB simulated 15k RPM drives
```
sudo vsim_makedisks -n 10 -t 36 -a 1
sudo vsim_makedisks -n 10 -t 36 -a 2
```
Additional disk types and sizes can be used, run vsim_makedisks –h to view options.

Reboot the node
```
sudo reboot
```
Hit space to interrupt the boot process 

## Node initialization
Set the following variable at the VLOADER then autoboot
```
setenv bootarg.setup.auto true
autoboot
```
The system will create a 3-disk root aggregate with enough capacity to allow us to upgrade ONTAP on the simulator.

Once booted, log in as admin with the password that was previously set.

If you plan to use more than one simulator, now would be a really good time to make a clone of the VM. (requires powering off or suspending the VM)

![image](https://github.com/DDTully/SimSetup/assets/165563299/8c09ae59-287a-42d8-9828-1eda0a923ae9)

## Upgrade ONTAP/Build Cluster
At this point you have two choices (both will require an HTTP server)
- Upgrade ONTAP now then build the cluster
- Build cluster now and then upgrade ONTAP

### Upgrade now

Make sure that the management lif is on a bridged port (either re-home the lif or change the Nic in the VM settings.

(replace webserver and package name with appropriate details)
```
set -confirmations off
set adv
system image update -package http://webserver/package.tgz -replace-package true -setdefault true
reboot
```

### Upgrade later
```
cluster setup
``` 
Follow the wizard to complete the cluster configuration.

Make sure that the management lif is on a bridged port (re-home the lif or change the Nic in the VM settings if needed)

Upgrade using either:

(replace webserver and package name with appropriate details)
```
set -confirmations off
set adv
system image update -package http://webserver/package.tgz -replace-package true -setdefault true
reboot
```
OR
```
set -confirmations off
set adv
cluster image package get -url http://webserver/package.tgz
cluster image update -version AppropriateVersion
```
Let it reboot.

## Complete Configuration
You should now have a fully functional ONTAP system at your disposal.  

Next steps:
- Install licenses
- Configure anything you want
- Take a snapshot of the VM (Highly recommended)
















