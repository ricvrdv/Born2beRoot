# 42 - BORN2BEROOT

### SUMMARY
This project was an introduction to the world of virtualization. The goal was to create a virtual machine (VM) under specific requirements.

It was only necessary to submit a text file with the SHA-1 checksum to verify the integrity of the VM.

### GUIDELINES

No graphical interface was allowed and the selected operating system (OS) was Debian GNU/Linux ([download page](https://www.debian.org/distrib/)).

The virtualization software (hypervisor) that was used was Oracle VirtualBox ([official site](https://www.virtualbox.org)).

### 1. INITIAL CONFIGURATION
---

- The memory size and the size of the virtual hard disk were set to 1024 MB and 30.8 GB, respectively.
- The partitions were set manually. The primary partition (sda1) was set to 500 MB and an encrypted logical partition (sda5) was created.
- The Logical Volume Manager (LVM) was configured creating a volume group named LVMGroup. Each logical volume was created and properly configured, selecting the file system (Ext4) and the respective mount point.
- With the OS fully installed and the disk properly partitioned the detailed partition structure can be shown by typing `lsblk` on the command line (see the image below for an example output).

<p align="center">
<img width="942" alt="lsblk_output" src="https://github.com/user-attachments/assets/aabe3ebd-bfd8-4ec7-a4ca-266e3d29a90a" />
</p>

- To check the OS installed: `$ uname -v`
- To see if there is no graphical interface: `$ ls /usr/bin/*session`

### 2. VIRTUAL MACHINE SETUP
---

#### 2.1 INSTALLING SUDO

1. Login as the root user:

`$ su`

2. Install sudo using the Advanced Packaging Tool (APT):

`$ apt install sudo`

3. Reboot the system to check if it was installed correctly:

`$ sudo reboot`

`$ sudo -V`

<br>

#### 2.2 ADDING USERS AND GROUPS

1. Adding user:

`$ sudo adduser <username>`

2. Adding group 'user42' as requested:

`$ sudo addgroup user42`

3. Adding the user to the groups 'sudo' and 'user42':

`$ sudo adduser <username> <groupname>`

4. Useful commands:

`$ getent group <groupname>` - to check if the user belongs to the group

`$ groups <username>` - to check the groups the user belongs to

`$ cat /etc/group` - to see the account details for all the existing groups

<br>

#### 2.3 INSTALLING AND CONFIGURING SECURE SHELL (SSH)

1. Updating the package management system:

`$ sudo apt update`

2. Installing OpenSSH server package:

`$ sudo apt install openssh-server`

3. Check the status of the SSH service:

`$ sudo service ssh status` or `$ sudo systemctl status ssh`

4. If the SSH service is not running:

`$ sudo service ssh start` or `$ sudo systemctl start ssh`

5. To enable the SSH service to start automatically on system boot:

`$ sudo systemctl enable ssh`

6. To run the SSH service on the port 4242:

`$ nano /etc/ssh/sshd_config` 

(-> Replace `#Port 22` with `#Port 4242` -> Edit `#PermitRootLogin prohibit-password` to `#PermitRootLogin no`)

`$ nano /etc/ssh/ssh_config`

(-> Replace `#Port 22` with `#Port 4242`

7. To update the changes on the previous files:

`$ sudo service ssh restart`

<br>

#### 2.4 INSTALLING AND CONFIGURING UNCOMPLICATED FIREWALL (UFW)

1. Install UFW:

`$ sudo apt install ufw`

`$ sudo ufw enable` - to enable the firewall on system startup

2. Adding a rule to allow incoming traffic on port 4242:

`$ sudo ufw allow 4242`

3. Check the new rule:

`$ sudo ufw status`

4. Check the status of the UFW:

`$ sudo service ufw status`

5. To delete a rule:

`$ sudo ufw status numbered` -> `$ sudo ufw delete <rule_number>`

<br>

#### 2.5 SET UP STRONG CONFIGURATION FOR SUDO GROUP

1. Creating a file to store the password configuration:

`$ touch /etc/sudoers.d/sudo_config`

2. Creating a sudo directory, where the input and output for each sudo-executed command will be stored:

`$ mkdir /var/log/sudo`

3. Editing `sudo_config` file:

`$ nano /etc/sudoers.d/sudo_config`

```
Defaults  passwd_tries=3
Defaults  badpass_message="Password is wrong, please try again."
Defaults  logfile="/var/log/sudo/sudo_config"
Defaults  log_input, log_output
Defaults  iolog_dir="/var/log/sudo"
Defaults  requiretty
Defaults  secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"
```

<br>

#### 2.6 SETTING STRONG PASSWORD POLICY

1. Edit `login.defs` file:

`$ nano /etc/login.defs`

(-> `PASS_MAX_DAYS 30`, `PASS_MIN_DAYS 2`, `PASS_WARN_AGE 7`)

2. Install PAM (Pluggable Authentication Module) to perform password quality checking:

`$ sudo apt install libpam-pwquality`

3. Edit `common_password` file:

`$ nano /etc/pam.d/common_password`

```
retry=3 minlen=10 ucredit=-1 lcredit=-1 dcredit=-1 maxrepeat=3 reject_username difok=7 enforce_for_root
```

(This new password policy will affect only the newly created users.)

4. To enforce the policy to the previously created users (including root):

`$ sudo chage -l <username>` - to list the information

`$ sudo chage -m 2 <username>` - to edit the min_days

`$ sudo chage -M 30 <username>` - to edit the max_days

<br>

#### 2.7 CONNECTING TO SSH

1. In VirtualBox go to Settings -> Network -> BridgedAdapter

2. Reboot the VM.

3. Find the VM IP:

`$ hostname -I`

4. While the VM is running open a Terminal window:

`$ ssh <username>@<IP> -p 4242`

5. Logout:

`$ exit`

<br>

### 3. SCRIPT AND CRONTAB
---

A script named `monitoring.sh`([see file](https://github.com/ricvrdv/Born2beRoot/blob/main/monitoring.sh)) was created in bash to display some information on all terminals every 10 minutes since the server startup.

#### 3.1 CREATING THE SCRIPT IN BASH

```
#!/bin/bash

while true; do

# ARCHITECTURE
arch=$(uname -a)

# PHYSICAL CPU
cpuf=$(grep "physical id" /proc/cpuinfo | wc -l)

# VIRTUAL CPU
cpuv=$(grep "processor" /proc/cpuinfo | wc -l)

# RAM
ram_total=$(free --mega | awk '$1 == "Mem:" {print $2}')
ram_use=$(free --mega | awk '$1 == "Mem:" {print $3}')
ram_percent=$(free --mega | awk '$1 == "Mem:" {printf("%.2f"), $3/$2*100}')

# DISK
disk_total=$(df -m | grep "/dev/" | grep -v "/boot" | awk '{disk_t += $2} END {printf ("%.1fGb\n"), disk_t/1024}')
disk_use=$(df -m | grep "/dev/" | grep -v "/boot" | awk '{disk_u += $3} END {print disk_u}')
disk_percent=$(df -m | grep "/dev/" | grep -v "/boot" | awk '{disk_u += $3} {disk_t+= $2} END {printf("%d"), disk_u/disk_t*100}')

# CPU LOAD
cpul=$(vmstat 1 2 | tail -1 | awk '{printf $15}')
cpu_op=$(expr 100 - $cpul)
cpu_fin=$(printf "%.1f" $cpu_op)

# LAST BOOT
lb=$(who -b | awk '$1 == "system" {print $3 " " $4}')

# LVM USE
lvmu=$(if [ $(lsblk | grep "lvm" | wc -l) -gt 0 ]; then echo yes; else echo no; fi)

# TCP CONNECTIONS
tcpc=$(ss -ta | grep ESTAB | wc -l)

# USER LOG
ulog=$(users | wc -w)

# NETWORK
ip=$(hostname -I)
mac=$(ip link | grep "link/ether" | awk '{print $2}')

# SUDO
cmnd=$(journalctl _COMM=sudo | grep COMMAND | wc -l)

wall "  Architecture: $arch
  CPU physical: $cpuf
	vCPU: $cpuv
	Memory Usage: $ram_use/${ram_total}MB ($ram_percent%)
	Disk Usage: $disk_use/${disk_total} ($disk_percent%)
	CPU load: $cpu_fin%
	Last boot: $lb
	LVM use: $lvmu
	Connections TCP: $tcpc ESTABLISHED
	User log: $ulog
	Network: IP $ip ($mac)
	Sudo: $cmnd cmd"

sleep 600
done
```
<br>

#### 3.2 CRONTAB

The cron table is used for scheduling and automating tasks.

1. Open crontab as root in the default text editor:

`$ sudo crontab -u root -e`

2. Edit the file:

```
@reboot <path for the script> &
```

<br>

### 4. VM SIGNATURE
---

To obtain the SHA-1 hash value that identifies the VM go to the directory where the VM is.

Then:

`$ sha1sum <VM Name>.vdi`

And save it in a `signature.txt` file.

---

----
🐸 Feel free to fork, clone, or contact me for questions or feedback. 











