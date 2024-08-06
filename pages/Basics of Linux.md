- #Commands
  collapsed:: true
	- Command Line
		- `ls` - list files
		- `cd` - change directory
		- `pwd` - print working directory
	- File Management
		- `cp` - copy files
		- `mv` - move files
		- `rm` - remove files
		- `touch` - create an empty file or update the timestamp of an existing file
			- `touch ~/.ssh/authorized_keys` - create an empty `authorized_keys` file in the user's `.ssh` directory
	- Package Management
		- `pip` - package installer for Python
		- `dnf` - package manager for RPM-based distributions (e.g., Fedora)
			- `dnf repolist` - display the list of available repositories and their status (RPM-based systems)
		- `apt` - package manager for Debian-based distributions (e.g., Ubuntu)
			- `apt-cache policy` - display the list of available repositories and their priorities (Debian-based systems)
		- Ubuntu Repositories
		  collapsed:: true
			- **Main**: Officially supported packages
			- **Universe**: Community-maintained packages
			- **Multiverse**: Packages that may include non-free software
			- **Restricted**: Restricted packages like drivers
			- Adding PPAs
				- **Example**
					- Add a PPA (Personal Package Archive):
						- Command: `sudo add-apt-repository ppa:repository-name/ppa`
						- Example: `sudo add-apt-repository ppa:deadsnakes/ppa`
					- Update the package list:
						- Command: `sudo apt-get update`
					- Install a package from the PPA:
						- Command: `sudo apt-get install package-name`
						- Example: `sudo apt-get install python3.9`
				- **PHP PPA**:
					- Add PPA: `sudo add-apt-repository ppa:ondrej/php`
					- Update package list: `sudo apt-get update`
					- Install PHP: `sudo apt-get install php7.4`
				- **MySQL PPA**:
					- Add PPA: `sudo add-apt-repository 'deb http://repo.mysql.com/apt/ubuntu/ focal mysql-5.7'`
					- Update package list: `sudo apt-get update`
					- Install MySQL: `sudo apt-get install mysql-server`
	- Text Editors
		- `nano` - a simple text editor for Unix-like systems
			- `nano ~/.ssh/authorized_keys` - open the `authorized_keys` file in the user's `.ssh` directory for editing
			- `nano /root/.ssh/authorized_keys` - open the `authorized_keys` file in the root's `.ssh` directory for editing
			- `nano /etc/ssh/sshd_config` - open the SSH daemon configuration file for editing
	- System Management
		- `systemctl` - control the systemd system and service manager
			- `systemctl daemon-reload`- Reload systemd configuration
			- `systemctl start [service]` - start a service
			- `systemctl stop [service]` - stop a service
			- `systemctl restart [service]` - restart a service
			- `systemctl status [service]` - check the status of a service
			- `systemctl enable [service]` - enable a service to start at boot
			- `systemctl disable [service]` - disable a service from starting at boot
		- **Unit** - A logical representation of a system resource or service managed by `systemd`.
			- **Types of Units**:
				- **Service Unit** (`.service`): Represents a service (daemon).
				- **Socket Unit** (`.socket`): Represents a network socket.
				- **Device Unit** (`.device`): Represents a device recognized by the system.
				- **Mount Unit** (`.mount`): Represents a mount point.
				- **Automount Unit** (`.automount`): Represents an automount point.
				- **Swap Unit** (`.swap`): Represents a swap partition.
				- **Target Unit** (`.target`): Represents a group of units.
				- **Timer Unit** (`.timer`): Represents a timer for scheduling tasks.
				- **Path Unit** (`.path`): Represents a file or directory to monitor for changes.
				- **Slice Unit** (`.slice`): Represents a group of resources (e.g., process groups).
				- **Scope Unit** (`.scope`): Represents external tasks not started by `systemd`.
			- **Example Service Unit** (`/etc/systemd/system/example.service`):
			  ```ini
			  [Unit]
			  Description=Example Service
			  After=network.target
			  
			  [Service]
			  ExecStart=/usr/bin/example-command
			  Restart=always
			  
			  [Install]
			  WantedBy=multi-user.target
			  ```
		- `apt-get` - Advanced Package Tool (stable interface for scripting)(old version)
		- `apt` - Advanced Package Tool (interactive interface)
			- `sudo apt update` - update the list of available packages
			- `sudo apt upgrade` - upgrade all installed packages
			- `sudo apt install [package_name]` - install a package
			- `sudo apt remove [package_name]` - remove a package
			- `apt search [package_name]` - search for a package
	- SSH Management
		- `ssh-keygen` - generate SSH key pairs for authentication
			- `ssh-keygen -t rsa -b 4096 -C "your_email@example.com"` - generate a new RSA key pair with 4096 bits and a specified comment
	- Network
		- `ip a` - display all IP addresses assigned to all network interfaces
		- `route -n` - display the kernel routing tables
		- `ping` - send ICMP ECHO_REQUEST to network hosts
		- `ifconfig` - configure a network interface
			- `ifconfig [interface]` - display configuration of a specific network interface
			- `ifconfig [interface] up` - activate a network interface
			- `ifconfig [interface] down` - deactivate a network interface
		- `netstat` - network statistics
			- `netstat -a` - display all active connections and listening ports
			- `netstat -r` - display the routing table
			- `netstat -i` - display network interface statistics
			- `netstat -tulnp` - show listening ports
				- `-t` - show TCP connections
				- `-u` - show UDP connections
				- `-l` - show listening ports
				- `-n` - show numeric addresses instead of resolving names
				- `-p` - show the PID and name of the program to which each socket belongs
		- `systemctl restart NetworkManager` - restart the NetworkManager service
	- Version Control
		- `git` - distributed version control system
- #User_and_Group_Management
  collapsed:: true
	- `useradd` - create a new user
		- `-c "comment"` - add a comment (usually the user's full name)
			- Example: `useradd -c "John Doe" johndoe`
		- `-d /home/dir` - set the user's home directory
			- Example: `useradd -d /home/johndoe johndoe`
		- `-e YYYY-MM-DD` - set the expiration date for the account
			- Example: `useradd -e 2023-12-31 johndoe`
		- `-g group` - set the primary group for the user
			- Example: `useradd -g users johndoe`
		- `-G groups` - set the supplementary groups for the user
			- Example: `useradd -G wheel,audio johndoe`
		- `-m` - create the user's home directory if it does not exist
			- Example: `useradd -m johndoe`
		- `-p password` - set the user's encrypted password (use with caution)
			- Example: `useradd -p $(openssl passwd -crypt 'password') johndoe`
		- `-s shell` - set the user's login shell
			- Example: `useradd -s /bin/bash johndoe`
		- `-u UID` - set the user ID for the user
			- Example: `useradd -u 1001 johndoe`
	- `groupadd` - create a new group
		- `-f` - do not fail if the group already exists
			- Example: `groupadd -f developers`
		- `-g GID` - set the group ID for the group
			- Example: `groupadd -g 1001 developers`
		- `-r` - create a system group (with a GID less than 1000)
			- Example: `groupadd -r developers`
	- `usermod` - modify a user account
		- `-c "comment"` - change the user's comment (usually full name)
			- Example: `usermod -c "John Doe" johndoe`
		- `-d /new/home/dir` - change the user's home directory
			- Example: `usermod -d /new/home/dir johndoe`
		- `-d /new/home/dir -m` - change the home directory and move current contents
			- Example: `usermod -d /new/home/dir -m johndoe`
		- `-e YYYY-MM-DD` - set the account expiration date
			- Example: `usermod -e 2024-12-31 johndoe`
		- `-g group` - change the user's primary group
			- Example: `usermod -g newgroup johndoe`
		- `-G groups` - change the user's supplementary groups (replaces current groups)
			- Example: `usermod -G group1,group2 johndoe`
		- `-aG group` - add the user to supplementary groups (does not replace current groups)
			- Example: `usermod -aG newgroup johndoe`
		- `-aG sudo user`
			- **sudo group**: In Debian-based systems, the `sudo` group is used to grant administrative privileges to its members. Users in this group can use the `sudo` command to execute commands as the superuser.
		- `-l newname` - change the user's login name
			- Example: `usermod -l newname oldname`
		- `-L` - lock the user's account
			- Example: `usermod -L johndoe`
		- `-U` - unlock the user's account
			- Example: `usermod -U johndoe`
		- `-s shell` - change the user's login shell
			- Example: `usermod -s /bin/zsh johndoe`
		- `-u UID` - change the user's user ID
			- Example: `usermod -u 1001 johndoe`
	- `passwd` - change user passwords and manage password properties
		- Change password for the current user:
			- Command: `passwd`
		- Change password for another user (requires superuser privileges):
			- Command: `sudo passwd username`
		- Lock a user account:
			- Command: `sudo passwd -l username`
		- Unlock a user account:
			- Command: `sudo passwd -u username`
		- Expire a user's password (force change on next login):
			- Command: `sudo passwd -e username`
		- Set the maximum number of days a password is valid:
			- Command: `sudo passwd -x days username`
		- Force user to change password on next login:
			- Command: `sudo passwd -f username`
	- `deluser` - remove a user account
		- Remove a user account:
			- Command: `sudo deluser username`
			- Example: `sudo deluser johndoe`
		- Remove a user account and home directory:
			- Command: `sudo deluser --remove-home username`
			- Example: `sudo deluser --remove-home johndoe`
		- Remove a user account and all files:
			- Command: `sudo deluser --remove-all-files username`
			- Example: `sudo deluser --remove-all-files johndoe`
		- Remove a user from a group:
			- Command: `sudo deluser username groupname`
			- Example: `sudo deluser johndoe sudo`
		- Force removal of a user:
			- Command: `sudo deluser --force username`
			- Example: `sudo deluser --force johndoe`
- #Programs
  collapsed:: true
	- **NetworkManager** - Service for managing network connections in Linux
		- Features: Automatic network configuration, support for various network types (Ethernet, Wi-Fi, mobile, VPN), graphical interfaces, and command-line tools.
		- **Installation**:
			- Install NetworkManager on Ubuntu:
				- Command: `sudo apt-get update`
				- Command: `sudo apt-get install network-manager`
			- Start and enable NetworkManager service:
				- Command: `sudo systemctl start NetworkManager`
				- Command: `sudo systemctl enable NetworkManager`
		- **`nmcli` (Network Manager Command Line Interface)**:
			- A command-line tool for managing network connections via NetworkManager.
			- **Key Features**:
				- Manage network connections (create, modify, delete, activate).
				- Configure network interfaces (enable, disable, view status).
				- Monitor network status and view available networks.
			- **Basic Commands**:
				- **General Status**:
					- Check NetworkManager status:
						- Command: `nmcli general status`
				- **Connection Management**:
					- List all connections:
						- Command: `nmcli connection show`
					- Add a new connection:
						- Command: `sudo nmcli connection add con-name 'dhcp' type ethernet ifname enp0s3`
					- Delete a connection:
						- Command: `sudo nmcli connection del 'dhcp'`
					- Bring a connection up:
						- Command: `nmcli connection up 'dhcp'`
					- Bring a connection down:
						- Command: `nmcli connection down 'dhcp'`
				- **Wi-Fi Management**:
					- List available Wi-Fi networks:
						- Command: `nmcli device wifi list`
					- Connect to a Wi-Fi network:
						- Command: `nmcli device wifi connect 'SSID' password 'your_password'`
				- **Device Management**:
					- List all network devices:
						- Command: `nmcli device status`
					- Enable a network device:
						- Command: `nmcli device set enp0s3 managed yes`
					- Disconnect a network device:
						- Command: `nmcli device disconnect enp0s3`
		- **nmtui** - Text user interface for NetworkManager
			- Start nmtui:
				- Command: `sudo nmtui`
			- Main options in nmtui:
				- Edit a connection
				- Activate a connection
				- Set system hostname
	- Network Management
		- `wget` - non-interactive network downloader
		- `net-tools`  -
		- **Adding a new Ethernet connection with DHCP using  `nmcli`**:
			- Command: `sudo nmcli connection add con-name 'dhcp' type ethernet ifname enp0s3`
			- **Explanation**:
				- `nmcli`: Command-line tool for interacting with NetworkManager.
				- `connection add`: Subcommand to add a new network connection.
				- `con-name 'dhcp'`: Sets the name of the new connection to 'dhcp'.
				- `type ethernet`: Specifies that the connection type is Ethernet (wired connection).
				- `ifname enp0s3`: Specifies the network interface name to which the connection will be applied (in this case, `enp0s3`).
			- **Purpose**: This command adds a new Ethernet connection named 'dhcp' to the network interface `enp0s3`, allowing it to use DHCP for obtaining an IP address and other network settings.
		- **Deleting a network connection using nmcli**:
			- Command: `sudo nmcli connection del 'dhcp'`
			- **Explanation**:
				- `nmcli`: Command-line tool for interacting with NetworkManager.
				- `connection del`: Subcommand to delete a network connection.
				- `'dhcp'`: Name of the connection to be deleted.
			- **Purpose**: This command deletes the network connection named 'dhcp'.
		- **Adding a static Ethernet connection using nmcli**:
			- Command: `nmcli connection add con-name 'static' ifname enp0s3 autoconnect no type ethernet ip4 192.168.200.52/24 gw4 192.168.200.1`
			- **Explanation**:
				- `connection add`: Subcommand to add a new network connection.
				- `con-name 'static'`: Sets the name of the new connection to 'static'.
				- `ifname enp0s3`: Specifies the network interface name (in this case, `enp0s3`).
				- `autoconnect no`: Specifies that the connection should not automatically connect on boot.
				- `type ethernet`: Specifies that the connection type is Ethernet (wired connection).
				- `ip4 192.168.200.52/24`: Sets the static IPv4 address for this connection.
				- `gw4 192.168.200.1`: Sets the gateway address for this connection.
			- **Purpose**: This command adds a new static Ethernet connection with a specified IP address and gateway.
		- **Activating a network connection using nmcli**:
			- Command: `nmcli con up 'static'`
			- **Explanation**:
				- `nmcli`: Command-line tool for interacting with NetworkManager.
				- `con up`: Subcommand to bring up (activate) a network connection.
				- `'static'`: Name of the connection to be activated.
			- **Purpose**: This command activates the network connection named 'static'.
- #System_Monitoring
  collapsed:: true
	- **Load Average**:
		- **Definition**: A metric that shows the average number of processes waiting for execution or CPU resources in the system. It is measured over three time intervals: 1, 5, and 15 minutes.
		- **Time Intervals**:
			- **1 minute**: Load average over the last 1 minute.
			- **5 minutes**: Load average over the last 5 minutes.
			- **15 minutes**: Load average over the last 15 minutes.
		- **Understanding Values**:
			- **1.0 on a single-core system**: The system is fully loaded (one process is running or waiting).
			- **1.0 on a multi-core system (e.g., 4 cores)**: The system is underutilized (4 cores can handle one process easily).
		- **Interpreting Values**:
			- **Less than 1.0 per core**: The system is functioning normally and is not overloaded.
			- **Equal to 1.0 per core**: The system is loaded but not overloaded.
			- **Greater than 1.0 per core**: The system is overloaded, and some processes must wait.
		- **Commands to Check Load Average**:
			- **`uptime`**:
				- Example: `uptime`
			- **`top`**:
				- Example: `top`
			- **`cat /proc/loadavg`**:
				- Example: `cat /proc/loadavg`
		- **Example Outputs**:
			- **`uptime`**:
			  ```plaintext
			  14:23:19 up 10 days,  2:53,  3 users,  load average: 0.15, 0.10, 0.05
			  ```
			- **`top`**:
			  ```plaintext
			  top - 14:23:19 up 10 days,  2:53,  3 users,  load average: 0.15, 0.10, 0.05
			  Tasks: 197 total,   1 running, 196 sleeping,   0 stopped,   0 zombie
			  %Cpu(s):  0.7 us,  0.3 sy,  0.0 ni, 99.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
			  ```
			- **`cat /proc/loadavg`**:
			  ```plaintext
			  0.15 0.10 0.05 1/197 4567
			  ```
	- **`iotop`** - A utility for monitoring I/O usage by processes in real time.
		- **Features**:
			- Displays current I/O activity for each process.
			- Filters active processes using I/O.
			- Shows cumulative I/O activity since `iotop` started.
		- **Installation**:
			- Debian-based systems:
				- Command: `sudo apt-get update`
				- Command: `sudo apt-get install iotop`
			- Red Hat-based systems:
				- Command: `sudo yum install iotop`
		- **Examples**:
			- Monitor all processes:
				- Command: `sudo iotop`
			- Filter active processes:
				- Command: `sudo iotop -o`
			- Show cumulative I/O:
				- Command: `sudo iotop -a`
	- **`iostat`** - A utility for monitoring system I/O statistics.
		- **Features**:
			- Displays CPU usage statistics.
			- Shows I/O statistics for devices and partitions.
			- Helps understand system resource utilization.
		- **Installation**:
			- Debian-based systems:
				- Command: `sudo apt-get update`
				- Command: `sudo apt-get install sysstat`
			- Red Hat-based systems:
				- Command: `sudo yum install sysstat`
		- **Examples**:
			- Basic command:
				- Command: `iostat`
			- Refresh statistics every 2 seconds:
				- Command: `iostat 2`
			- Refresh statistics every 2 seconds, 5 times:
				- Command: `iostat 2 5`
			- Display device statistics only:
				- Command: `iostat -d`
			- Display CPU statistics only:
				- Command: `iostat -c`
			- Display extended device statistics:
				- Command: `iostat -x`
			- Display statistics for a specific device:
				- Command: `iostat -d sda`
		- **Explanation of Output**:
			- **avg-cpu**: Average CPU usage statistics.
				- **%user**: Percentage of CPU time spent on user processes.
				- **%nice**: Percentage of CPU time spent on low-priority user processes.
				- **%system**: Percentage of CPU time spent on system (kernel) processes.
				- **%iowait**: Percentage of CPU time spent waiting for I/O operations.
				- **%steal**: Percentage of CPU time spent waiting for the hypervisor (in virtualized environments).
				- **%idle**: Percentage of CPU time spent idle.
			- **Device statistics**:
				- **tps**: Transactions per second (I/O operations per second).
				- **kB_read/s**: Kilobytes read per second.
				- **kB_wrtn/s**: Kilobytes written per second.
				- **kB_read**: Total kilobytes read.
				- **kB_wrtn**: Total kilobytes written.
	- **`lsof`** - A command-line utility to list open files and the processes that opened them.
		- **Features**:
			- Displays all open files in the system.
			- Filters open files by user, process, or file.
			- Monitors active network connections and sockets.
		- **Installation**:
			- Debian-based systems:
				- Command: `sudo apt-get update`
				- Command: `sudo apt-get install lsof`
			- Red Hat-based systems:
				- Command: `sudo yum install lsof`
		- **Examples**:
			- Display all open files:
				- Command: `lsof`
			- Display open files for a specific user:
				- Command: `lsof -u username`
			- Display open files for a specific process:
				- Command: `lsof -p PID`
			- Display processes that opened a specific file:
				- Command: `lsof /path/to/file`
			- Display open files in a specific directory:
				- Command: `lsof +D /path/to/directory`
			- Display network connections:
				- Command: `lsof -i`
			- Display processes using a specific port:
				- Command: `lsof -i :80`
		- **Example Output**:
		  ```plaintext
		  COMMAND     PID    USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
		  init          1    root  cwd    DIR  259,1     4096    2 /
		  init          1    root  rtd    DIR  259,1     4096    2 /
		  init          1    root  txt    REG  259,1  1599080 4373 /sbin/init
		  bash        1313   user  cwd    DIR  259,1     4096  641 /home/user
		  bash        1313   user  txt    REG  259,1  1113504 1496 /bin/bash
		  sshd        2586   root  cwd    DIR  259,1     4096    2 /
		  sshd        2586   root  txt    REG  259,1   941960 1453 /usr/sbin/sshd
		  sshd        3014   user  cwd    DIR  259,1     4096  641 /home/user
		  sshd        3014   user  txt    REG  259,1   941960 1453 /usr/sbin/sshd
		  ```
	- **`df -hT`** - Command to display disk space usage and filesystem types.
		- **Options**:
			- `-h`: Display information in a human-readable format.
			- `-T`: Show the filesystem type.
		- **Example**:
			- Command: `df -hT`
			- Output:
			  ```plaintext
			  Filesystem     Type      Size  Used Avail Use% Mounted on
			  udev           devtmpfs  3.9G     0  3.9G   0% /dev
			  tmpfs          tmpfs     798M  1.3M  797M   1% /run
			  /dev/sda1      ext4       50G   15G   32G  32% /
			  tmpfs          tmpfs     3.9G  220K  3.9G   1% /dev/shm
			  tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
			  tmpfs          tmpfs     798M   44K  798M   1% /run/user/1000
			  ```
- #File_Management
  collapsed:: true
	- **`rm -rf /nmt/junkdirectory/`** - Command to forcefully and recursively remove a directory and its contents.
		- **Options**:
			- `-r` (recursive): Recursively remove directories and their contents.
			- `-f` (force): Force removal without confirmation.
		- **Example**:
			- Command: `rm -rf /nmt/junkdirectory/`
		- **Caution**: This command permanently deletes the directory and its contents without asking for confirmation.
	- **`sshfs`** - A tool to mount remote filesystems over SSH.
		- **Command Example**:
			- Command: `sudo sshfs dev@192.168.200.52:/mnt/backup /mnt/backup_remote -o port=33555,allow_other`
		- **Explanation**:
			- **`sudo`**: Executes the command with superuser privileges.
			- **`sshfs`**: Utility to mount a remote filesystem over SSH.
			- **`dev@192.168.200.52:/mnt/backup`**: Remote directory to be mounted.
				- `dev`: Username on the remote server.
				- `192.168.200.52`: IP address of the remote server.
				- `/mnt/backup`: Directory on the remote server to mount.
			- **`/mnt/backup_remote`**: Local mount point.
			- **`-o`**: Mount options.
				- **`port=33555`**: Specifies that SSH should use port 33555 instead of the default port 22.
				- **`allow_other`**: Allows other users to access the mounted filesystem.
		- **Useful Commands**:
			- **Mounting a remote filesystem**:
				- Command: `sudo sshfs user@remote_host:/remote/directory /local/mountpoint -o options`
			- **Unmounting a remote filesystem**:
				- Command: `sudo fusermount -u /local/mountpoint`
- #Network_Management
  collapsed:: true
	- **`iptables`** - A powerful utility for configuring network packet filtering rules in Linux.
		- **Tables**:
			- **filter**: Main table for packet filtering.
			- **nat**: Table for Network Address Translation (NAT).
			- **mangle**: Table for packet alteration.
			- **raw**: Table for raw packets (used for connection tracking exemptions).
			- **security**: Table for SELinux.
		- **Chains**:
			- **INPUT**: Processes incoming packets destined for the local system.
			- **OUTPUT**: Processes outgoing packets sent by the local system.
			- **FORWARD**: Processes packets passing through the system (routed).
			- **PREROUTING**: Processes packets before routing.
			- **POSTROUTING**: Processes packets after routing.
		- **Targets**:
			- **ACCEPT**: Allows the packet.
			- **DROP**: Drops the packet.
			- **REJECT**: Drops the packet and sends an error message to the sender.
			- **LOG**: Logs the packet.
			- **MASQUERADE**: Performs masquerading (commonly used in NAT).
		- **Basic Commands**:
			- **List current rules**:
				- Command: `sudo iptables -L`
			- **Add a rule**:
				- Command: `sudo iptables -A [CHAIN] -p [PROTOCOL] --dport [PORT] -j [TARGET]`
				- Example: Allow incoming connections on port 22 (SSH):
				  ```sh
				  sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
				  ```
			- **Delete a rule**:
				- Command: `sudo iptables -D [CHAIN] [RULE_NUMBER]`
				- Example: Delete the first rule in the INPUT chain:
				  ```sh
				  sudo iptables -D INPUT 1
				  ```
			- **Save rules**:
				- Command: `sudo sh -c "iptables-save > /etc/iptables/rules.v4"`
			- **Restore rules**:
				- Command: `sudo iptables-restore < /etc/iptables/rules.v4`
		- **Example Rules**:
			- **Allow incoming connections on port 80 (HTTP)**:
				- Command: `sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT`
			- **Block all incoming connections**:
				- Command: `sudo iptables -P INPUT DROP`
			- **Allow outgoing connections**:
				- Command: `sudo iptables -A OUTPUT -j ACCEPT`
			- **Log dropped packets**:
				- Command: `sudo iptables -A INPUT -j LOG --log-prefix "iptables: "`
	- **`nftables`** - A modern packet filtering framework to replace `iptables`, `ip6tables`, `arptables`, and `ebtables`.
		- **Features**:
			- Unified utility for managing network packet filtering.
			- Supports IPv4, IPv6, ARP, and other protocols.
			- Simplified and flexible syntax for rules.
			- Optimized performance and reduced system load.
			- Supports sets for grouping addresses and other objects.
		- **Installation**:
			- Debian-based systems:
				- Command: `sudo apt-get update`
				- Command: `sudo apt-get install nftables`
		- **Basic Commands**:
			- **Start and enable nftables service**:
				- Command: `sudo systemctl start nftables`
				- Command: `sudo systemctl enable nftables`
			- **Create a new table**:
				- Command: `sudo nft add table inet filter`
			- **Create a new chain**:
				- Command: `sudo nft add chain inet filter input { type filter hook input priority 0 \; }`
			- **Add a rule**:
				- Command: `sudo nft add rule inet filter input tcp dport 22 accept`
			- **List current rules**:
				- Command: `sudo nft list ruleset`
			- **Delete a rule**:
				- Command: `sudo nft delete rule inet filter input handle [handle_number]`
		- **Example Setup**:
			- **Create filter table**:
				- Command: `sudo nft add table inet filter`
			- **Create input chain**:
				- Command: `sudo nft add chain inet filter input { type filter hook input priority 0 \; }`
			- **Create forward chain**:
				- Command: `sudo nft add chain inet filter forward { type filter hook forward priority 0 \; }`
			- **Create output chain**:
				- Command: `sudo nft add chain inet filter output { type filter hook output priority 0 \; }`
			- **Add rules**:
				- Allow incoming SSH connections on port 22:
					- Command: `sudo nft add rule inet filter input tcp dport 22 accept`
				- Block all incoming connections by default:
					- Command: `sudo nft add rule inet filter input drop`
				- Allow outgoing connections:
					- Command: `sudo nft add rule inet filter output accept`
				- Log dropped packets:
					- Command: `sudo nft add rule inet filter input drop log prefix "nftables: "`
		- **`nftables` Practical Tips**
			- **Automation with Scripts**
				- Create a script:
					- Command: `sudo nano /etc/nftables.conf`
				- Example script content:
				  ```plaintext
				  #!/usr/sbin/nft -f
				  
				  table inet filter {
				      chain input {
				          type filter hook input priority 0; policy drop;
				  
				          # Allow established and related connections
				          ct state established,related accept
				  
				          # Allow loopback traffic
				          iif lo accept
				  
				          # Allow SSH
				          tcp dport 22 accept
				  
				          # Allow HTTP and HTTPS
				          tcp dport { 80, 443 } accept
				  
				          # Log and drop other traffic
				          log prefix "nftables: input drop: " flags all counter
				          drop
				      }
				  
				      chain forward {
				          type filter hook forward priority 0; policy drop;
				      }
				  
				      chain output {
				          type filter hook output priority 0; policy accept;
				      }
				  }
				  
				  table ip nat {
				      chain prerouting {
				          type nat hook prerouting priority -100; policy accept;
				      }
				  
				      chain postrouting {
				          type nat hook postrouting priority 100; policy accept;
				          ip saddr 192.168.1.0/24 oif eth0 masquerade
				      }
				  }
				  ```
				- Apply the script:
					- Command: `sudo nft -f /etc/nftables.conf`
				- Enable automatic application on boot:
					- Command: `sudo systemctl enable nftables`
			- **Using Sets**
				- Create a set for allowed IP addresses:
					- Command: `sudo nft add set inet filter allowed_ips { type ipv4_addr\; }`
				- Add IP addresses to the set:
					- Command: `sudo nft add element inet filter allowed_ips { 192.168.1.100, 192.168.1.101 }`
				- Use the set in rules:
					- Command: `sudo nft add rule inet filter input ip saddr @allowed_ips accept`
			- **Managing NAT**
				- Create NAT table and chains:
					- Command: `sudo nft add table ip nat`
					- Command: `sudo nft add chain ip nat prerouting { type nat hook prerouting priority -100 \; }`
					- Command: `sudo nft add chain ip nat postrouting { type nat hook postrouting priority 100 \; }`
				- Add masquerading rule:
					- Command: `sudo nft add rule ip nat postrouting oif eth0 masquerade`
			- **Logging Traffic**
				- Add logging rule for incoming traffic:
					- Command: `sudo nft add rule inet filter input log prefix "nftables: input: " flags all counter`
			- **DDoS Protection**
				- Rate limit incoming SSH traffic:
					- Command: `sudo nft add rule inet filter input tcp dport 22 ct state new limit rate 15/minute accept`
					- Command: `sudo nft add rule inet filter input tcp dport 22 drop`
- #Disk_Management
  collapsed:: true
	- **RAID** - Redundant Array of Independent Disks
		- **Levels**:
			- **RAID 0**: Striping, no redundancy.
			- **RAID 1**: Mirroring, high redundancy.
			- **RAID 5**: Striping with parity, single disk fault tolerance.
			- **RAID 6**: Striping with double parity, dual disk fault tolerance.
			- **RAID 10**: Combination of RAID 1 and RAID 0.
	- **`mdadm`** - Utility for managing software RAID arrays in Linux.
		- **Commands**:
			- Create RAID array:
				- Command: `sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sda1 /dev/sdb1`
			- Add device to RAID array:
				- Command: `sudo mdadm --manage /dev/md0 --add /dev/sdc1`
			- Remove device from RAID array:
				- Command: `sudo mdadm --manage /dev/md0 --fail /dev/sdb1`
				- Command: `sudo mdadm --manage /dev/md0 --remove /dev/sdb1`
			- View RAID array status:
				- Command: `sudo mdadm --detail /dev/md0`
			- Create configuration file:
				- Command: `sudo mdadm --detail --scan | sudo tee -a /etc/mdadm/mdadm.conf`
			- Reassemble RAID array:
				- Command: `sudo mdadm --assemble --scan`
	- **`mkfs`** - Make File System, utility to create a filesystem on a partition or logical volume.
		- **Commands**:
			- Create ext4 filesystem:
				- Command: `sudo mkfs.ext4 /dev/sda1`
			- Create xfs filesystem:
				- Command: `sudo mkfs.xfs /dev/sda1`
			- Create vfat (FAT32) filesystem:
				- Command: `sudo mkfs.vfat /dev/sda1`
	- **`fdisk`** - Utility to create, modify, and delete partitions on hard drives.
		- **Commands**:
			- Start `fdisk` for a device:
				- Command: `sudo fdisk /dev/sda`
			- **Interactive Commands**:
				- **`p`**: Print partition table.
				- **`n`**: Create a new partition.
				- **`d`**: Delete a partition.
				- **`t`**: Change partition type.
				- **`w`**: Write changes to disk and exit.
				- **`q`**: Quit without saving changes.
			- **Example**:
				- Start `fdisk`: `sudo fdisk /dev/sda`
				- Create a new partition: Press `n`, select type, number, start, and end sectors.
				- Write changes: Press `w`.
- #Log_Management
  collapsed:: true
	- **`/etc/logrotate.d/nginx`** - Configuration file for `logrotate` to manage Nginx log files.
		- **Example Content**:
		  ```plaintext
		  /var/log/nginx/*.log {
		      daily
		      missingok
		      rotate 14
		      compress
		      delaycompress
		      notifempty
		      create 0640 www-data adm
		      sharedscripts
		      postrotate
		          if [ -f /var/run/nginx.pid ]; then
		              kill -USR1 `cat /var/run/nginx.pid`
		          fi
		      endscript
		  }
		  ```
		- **Explanation**:
			- **/var/log/nginx/*.log**: Specifies the log files to rotate.
			- **daily**: Rotate logs daily.
			- **missingok**: Ignore missing log files and do not throw an error.
			- **rotate 14**: Keep 14 old log files before deleting them.
			- **compress**: Compress rotated log files using gzip.
			- **delaycompress**: Delay compression until the next rotation.
			- **notifempty**: Do not rotate empty log files.
			- **create 0640 www-data adm**: Create new log files with specified permissions, owner, and group.
			- **sharedscripts**: Ensure postrotate and prerotate scripts run only once.
			- **postrotate...endscript**: Script to execute after rotation. Sends USR1 signal to Nginx to reopen log files.
- #Disk_Performance_Testing
  collapsed:: true
	- **Command**: `sync; dd if=/dev/zero of=tempfile bs=2M count=2048; sync`
		- **Explanation**:
			- `sync`: Writes all cached data to disk, ensuring all file system changes are applied.
			- `dd if=/dev/zero of=tempfile bs=2M count=2048`: Creates a file named `tempfile` filled with zeros, with a total size of 4GB (2048 blocks of 2MB each).
			- `sync`: Ensures all data is written to disk after the file creation.
		- **Purpose**: This command sequence is used for testing disk performance by creating a large file and ensuring all data is written to disk without using the cache.
- #System_Performance_Testing
  collapsed:: true
	- **`sysbench`** - A multi-purpose benchmarking tool for system performance testing.
		- **Features**:
			- CPU performance testing
			- Memory performance testing
			- Disk I/O performance testing
			- Database performance testing (MySQL, PostgreSQL)
		- **Installation**:
			- Debian-based systems:
				- Command: `sudo apt-get update`
				- Command: `sudo apt-get install sysbench`
			- Red Hat-based systems:
				- Command: `sudo yum install sysbench`
		- **Examples**:
			- **CPU testing**:
				- Command: `sysbench --test=cpu --cpu-max-prime=20000 run`
			- **Memory testing**:
				- Command: `sysbench --test=memory --memory-block-size=1M --memory-total-size=10G run`
			- **Disk I/O testing**:
				- Prepare: `sysbench --test=fileio --file-total-size=10G prepare`
				- Run: `sysbench --test=fileio --file-total-size=10G --file-test-mode=rndrw run`
				- Cleanup: `sysbench --test=fileio --file-total-size=10G cleanup`
	- **`stress-ng`** - A tool for stress testing and benchmarking the system.
		- **Features**:
			- CPU stress testing
			- Memory stress testing
			- Disk I/O stress testing
			- Stress testing of various system resources
		- **Installation**:
			- Debian-based systems:
				- Command: `sudo apt-get update`
				- Command: `sudo apt-get install stress-ng`
			- Red Hat-based systems:
				- Command: `sudo yum install epel-release`
				- Command: `sudo yum install stress-ng`
		- **Examples**:
			- **CPU stress testing**:
				- Command: `stress-ng --cpu 4 --timeout 60s`
			- **Memory stress testing**:
				- Command: `stress-ng --vm 2 --vm-bytes 1G --timeout 60s`
			- **Disk I/O stress testing**:
				- Command: `stress-ng --hdd 2 --timeout 60s`
			- **Comprehensive system stress testing**:
				- Command: `stress-ng --cpu 4 --vm 2 --vm-bytes 1G --hdd 2 --timeout 60s`
- #System_Management
  collapsed:: true
	- **Resetting Root Password on Debian**
		- **Step 1: Reboot the Server**
			- Command: `sudo reboot`
			- Alternatively, press the physical reboot button if you have direct access.
		- **Step 2: Access GRUB Bootloader**
			- During the reboot, press `Esc` or `Shift` to access the GRUB menu.
		- **Step 3: Edit Boot Parameters**
			- Select the current kernel entry and press `e` to edit.
			- Add `single` or `init=/bin/bash` to the end of the `linux` line.
				- Example:
				  ```plaintext
				  linux /boot/vmlinuz-4.19.0-16-amd64 root=UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ro quiet single
				  ```
				  or
				  ```plaintext
				  linux /boot/vmlinuz-4.19.0-16-amd64 root=UUID=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx ro quiet init=/bin/bash
				  ```
			- Press `Ctrl` + `X` or `F10` to boot with the modified parameters.
		- **Step 4: Change Root Password**
			- If using `init=/bin/bash`, remount the filesystem as read-write:
				- Command: `mount -o remount,rw /`
			- Change the root password:
				- Command: `passwd root`
				- Enter the new password twice.
			- Synchronize and reboot:
				- Command: `sync`
				- Command: `reboot -f`