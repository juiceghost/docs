## This is the start of my initial server setup instructions

Guides followed:
* https://linuxconfig.org/how-to-install-and-configure-dropbear-on-linux
* https://linuxhint.com/advanced_ufw_firewall_configuration_ubuntu/
* https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands
* https://www.linuxnix.com/remove-ssh-server-linux/

# Pre-install tasks
* Sign in to dev.kjeld.io
* Escalate to root
```
yac@dev:~$ sudo su
[sudo] password for yac:
root@dev:/home/yac#
```
# Install tasks
* Use apt to install dropbear
```
root@dev:/home/yac# apt install dropbear
Reading package lists... Done
Building dependency tree
Reading state information... Done
dropbear is already the newest version (2019.78-2build1).
0 upgraded, 0 newly installed, 0 to remove and 8 not upgraded.
root@dev:/home/yac#
```
# Configuration
* Use nano to edit /etc/default/dropbear
```
root@dev:/home/yac# nano /etc/default/dropbear
```
* In the file we've made the following changes
```
(beginning of file)
# disabled because OpenSSH is installed
# change to NO_START=0 to enable Dropbear
NO_START=0 # was 1
# the TCP port that Dropbear listens on
DROPBEAR_PORT=30042 # was 22
10 # specify an optional banner file containing a message to be
11 # sent to clients before they connect, such as "/etc/issue.net"
12 DROPBEAR_BANNER="/etc/motd" # was ""
```
* Then i restarted the dropbear service for the changes to take effect
```
systemctl restart dropbear
```
* Next i verified from a separate local shell that dropbear is working
* and that i am able to logon using my custom port 30042
```
~ λ ssh -p 30042 dev.kjeld.io
...motd shows here
```
# Install howto for ufw (the firewall)
* I installed ufw on the machine as root
```
root@dev:/home/yac# apt install ufw
Reading package lists... Done
Building dependency tree
Reading state information... Done
ufw is already the newest version (0.36-6).
0 upgraded, 0 newly installed, 0 to remove and 8 not upgraded.
root@dev:/home/yac#
```
* ufw was already installed on this machine, so i check the status
```
root@dev:/home/yac# ufw status verbose
Status: inactive
root@dev:/home/yac#
```
* I allow port 30042 which is the port dropbear is configured to listen for ssh connections from
```
root@dev:/home/yac# ufw allow 30042/tcp
Rules updated
Rules updated (v6)
root@dev:/home/yac#
```
* I enabled ufw and checked its status
```
root@dev:/home/yac# ufw enable
Command may disrupt existing ssh connections. Proceed with operation (y|n)? y
Firewall is active and enabled on system startup
root@dev:/home/yac# ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), deny (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
30042/tcp                  ALLOW IN    Anywhere
30042/tcp (v6)             ALLOW IN    Anywhere (v6)

root@dev:/home/yac#
```
* I stop the built-in SSH service
```
root@dev:/home# systemctl stop ssh
root@dev:/home#
```
* I remove the openssh-server using apt
```
root@dev:/home# apt remove openssh-server
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following packages will be REMOVED:
  openssh-server ssh
0 upgraded, 0 newly installed, 2 to remove and 8 not upgraded.
After this operation, 1647 kB disk space will be freed.
Do you want to continue? [Y/n]
(Reading database ... 55697 files and directories currently installed.)
Removing ssh (1:8.2p1-4ubuntu0.3) ...
Removing openssh-server (1:8.2p1-4ubuntu0.3) ...
Processing triggers for man-db (2.9.1-1) ...
root@dev:/home#
```
* Next i reboot the server just to make sure everything still works after a fresh boot
```
root@dev:/home# shutdown -r now
root@dev:/home#
```
* I try logging in again after the reboot
```
~ λ ssh -p 30042 dev.kjeld.io
...
REMEMBER TO SIGN UP FOR THE HALLOWEEN PARTY!
/ERIK
yac@dev.kjeld.io's password:
There are 2 unique users online right now
yac@dev:~$
```





