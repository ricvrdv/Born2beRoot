# BORN2BEROOT

### [MILESTONE 1]
### SUMMARY
This project was an introduction to the world of visualization. The goal was to create a virtual machine (VM) under specific requirements.

It was only necessary to submit a text file with the SHA-1 checksum to verify the integrity of the VM.

### GUIDELINES

No graphical interface was allowed and the selected operating system (OS) was Debian GNU/Linux ([download page](https://www.debian.org/distrib/)).

The virtualization software (hypervisor) that was used was Oracle VirtualBox ([official site](https://www.virtualbox.org)).

#### 1. INITIAL CONFIGURATION
---

- The memory size and the size of the virtual hard disk were set to 1024 MB and 30.8 GB, respectively.
- The partitions were set manually. The primary partition (sda1) was set to 500 MB and an encrypted logical partition (sda5) was created.
- The Logical Volume Manager (LVM) was configured creating a volume group named LVMGroup. Each logical volume was created and properly configured, selecting the file system (Ext4) and the respective mount point (SEE IMAGE).
- With the OS fully installed and the disk properly partitioned the detailed partition structure can be shown by typing `lsblk` on the command line.
- To check the OS installed: `$ uname -v`
- To see if there is no graphical interface: `$ ls /usr/bin/*session`

#### 2. VIRTUAL MACHINE SETUP
---

#### 2.1 INSTALLING SUDO

- Login as the root user:

`$ su`

- Install sudo using the Advanced Packaging Tool (APT):

`$ apt install sudo`

- Reboot the system to check if it was installed correctly:

`$ sudo reboot`

`$ sudo -V`

#### 2.2 ADDING USERS AND GROUPS

- Adding user:

`$ sudo adduser <username>`

- Adding group 'user42' as requested:

`$ sudo addgroup user42`

- Adding the user to the groups 'sudo' and 'user42':

`$ sudo adduser <username> <groupname>`

- Useful commands:

`$ getent group <groupname>` - to check if the user belongs to the group

`$ groups <username>` - to check the groups the user belongs to

`$ cat /etc/group` - to see the account details for all the existing groups

#### 2.3 INSTALLING AND CONFIGURING SECURE SHELL (SSH)

- Updating the package management system:

`$ sudo apt update`

- Installing OpenSSH server package:

`$ sudo apt install openssh-server`

- Check the status of the SSH service:

`$ sudo service ssh status` or `$ sudo systemctl status ssh`

- If the SSH service is not running:

`$ sudo service ssh start` or `$ sudo systemctl start ssh`

- To enable the SSH service to start automatically on system boot:

`$ sudo systemctl enable ssh`

- To run the SSH service on the port 4242:

`$ nano /etc/ssh/sshd_config` 

(-> Replace `#Port 22` with `#Port 4242` -> Edit `#PermitRootLogin prohibit-password` to `#PermitRootLogin no`)

`$ nano /etc/ssh/ssh_config`

(-> Replace `#Port 22` with `#Port 4242`

- To update the changes on the previous files:

`$ sudo service ssh restart`

#### 2.4 INSTALLING AND CONFIGURING UNCOMPLICATED FIREWALL (UFW)

- Install UFW:

`$ sudo apt install ufw`

`$ sudo ufw enable` to enable the firewall on system startup

- Adding a rule to allow incoming traffic on port 4242:

`$ sudo ufw allow 4242`

- Check the new rule:

`$ sudo ufw status`

- Check the status of the UFW:

`$ sudo service ufw status`

- 




