# BORN2BEROOT

### [MILESTONE 1]
### SUMMARY
This project was an introduction to the world of visualization. The goal was to create a virtual machine (VM) under specific requirements.

It was only necessary to submit a text file with the SHA-1 checksum to verify the integrity of the VM.

### GUIDELINES

No graphical interface was allowed and the selected operating system (OS) was Debian GNU/Linux ([download page](https://www.debian.org/distrib/)).

The virtualization software (hypervisor) that was used was Oracle VirtualBox ([official site](https://www.virtualbox.org)).

#### 1. INITIAL CONFIGURATION

- The memory size and the size of the virtual hard disk were set to 1024 MB and 30.8 GB, respectively.
- The partitions were set manually. The primary partition (sda1) was set to 500 MB and an encrypted logical partition (sda5) was created.
- The Logical Volume Manager (LVM) was configured creating a volume group named LVMGroup. Each logical volume was created and properly configured, selecting the file system (Ext4) and the respective mount point (SEE IMAGE).
- With the OS fully installed and the disk properly partitioned the detailed partition structure can be shown by typing `lsblk` on the command line.

#### 2. VIRTUAL MACHINE SETUP

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
