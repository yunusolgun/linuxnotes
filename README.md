# linuxnotes
linux notes


# Network interface files:
	/etc/sysconfig/network-scripts/ifcfg-enp0s3
		DEVICE=enp0s3
		BOOTPROTO=dhcp
		ONBOOT=yes
		
	Static:
	/etc/sysconfig/network-scripts/ifcfg-enp0s3
		DEVICE=enp0s3
		BOOTPROTO=none
		ONBOOT=yes
		PREFIX=24
		IPADDR=10.0.1.27


# Network tools:		
	nmtui
	nmcli
	man mncli-examples
	sudo nmcli c reload
	sudo nmcli con load /etc/syscondif/netwok-scripts/ifcfg-enp0s3
	sudo nmcli dev disconnect enp0s3
	sudo nmcli con up enp0s3
	nmcli con show
	nmcli con show --active
	nmcli general status
	nmcli con edit



# Configure interface bonding
	sudo systemctl status NetworkManager
	nmcli dev status
	sudo modprobe bonding
	lsmod | grep bonding
	nmcli con add type bond con-name bond0 ifname bond0 mode balance-rr ip4 192.168.100.101/24 gw4 192.168.100.1
	cat /etc/sysconfig/network-scripts/ifcfg-bond0
	nmcli con add type bond-slave ifname ens9 master bond0
	nmcli con add type bond-slave ifname ens10 master bond0
	cat /etc/sysconfig/network-scripts/ifcfg-bond0-slave-ens9
	nmcli con up bond0
	ip addr | grep bond0
	nmcli con show | grep bond



# List, create and delte partitions on MBR and GPT disks
	cat /proc/partittions
	lsblk
	sudo fdsisk -l
	sudo fdsisk -l /dev/sdb
	fdisk
	gdisk
	parted
	sudo gdisk /dev/sdb
	sudo gdisk -l /dev/sdb
	sudo partprobe
	sudo mkfs -t ext4 /dev/sdb1



# Create partitions using parted
	sudo yum install -y parted
	sudo parted



# Resize partitions without losing data
	sudo dd if=/dev/sdc of=/home/user1/sdc-mbr.bin bs=512 count=1
	sudo dd if=//home/user1/sdc-mbr.bin of=/dev/sdc
	sudo gdisk /dev/sdb


# Manage LVM volumes and volume groups
	sudo umount /dev/sdb1
	sudo pvcreate /dev/sdb1
	sudo pvs
	sudo vgcreate vgdata /dev/sdb1
	sudo vgs
	sudo lvcreate --name lvdata --size 500M vgdata
	sudo lvs
	sudo vgchange -ay
	sudo mkfs -t ext4 /dev/vgdata/lvdata
	sudo blkid
	sudo mkdir /media/lvdata
	sudo mount /dev/vgdata/lvdata /media/lvdata
	lsblk




# Extend existing logical volumes
	lsblk
	sudo pvcreate /dev/sdc1
	sudo pvs
	sudo vgs
	sudo cgextend cgdata /dev/sdc1
	sudo vgs
	sudo lvs
	sudo lvresize -l 100%FREE /dev/vgdata/lvdata
	sudo lvs
	sudo resize2fs /dev/vgdata/lvdata




# Reduce existing logical volumes
	sudo umount /dev/vgdata/lvdata
	sudo e2fsck -ff /dev/vgdata/lvdata
	sudo resize2fs /dev/vgdata/??vdata 500M
	sudo lvresize -L 500M /dev/vdata/lvdata
	sudo lvs
	sudo mount /dev/vgdata/lvdata /media/lvdata
	sudo lvresize -r -L 400M /dev/vgdata/lvdata
	sudo lvresize -r -L 600M /dev/vgdata/lvdata



# Replace a physical volume
	sudo lsblk
	sudo pvs
	sudo lvresize -r -l 100%VG /dev/vgdata/lvdata
	sudo lvs
	sudo gdisk /dev/sdd
	sudo lsblk
	sudo vgextend vgdata /dev/sdd1
	sudo vgs
	sudo pvs
	sudo pvmove /dev/sdc1 /dev/sdd1
	sudo pvs
	sudo vgreduce vgdata /dev/sdc1
	sudo pvs



# Create EXT filesystems
	ls /sbin/mk*
	sudo umount /dev/vgdata/lvdata
	df
	sudo mkfs /dev/vgdata/lvdata
	lsblk -f
	sudo tune2fs -j /dev/vgdata/lvdata
	sudo lsblk -f
	sudo mount -t ext4 /dev/vgdata/lvdata /media/lvdata
	sudo df -T



# Repair EXT filesystems
	sudo mount /dev/vgdata/lvdata /media/lvdata
	df
	sudo dd if=/dev/zero bs=1 count=10 of=/dev/vgdata/lvdata seek=1000
	sudo dd if=/dev/zero bs=1 count=10 of=/dev/vgdata/lvdata seek=1200
	sudo dd if=/dev/zero bs=1 count=10 of=/dev/vgdata/lvdata seek=1122
	sudo dd if=/dev/zero bs=1 count=10 of=/dev/vgdata/lvdata seek=2400
	sudo umount /media/lvdata
	sudo fsck -n /dev/vgdata/lvdata



# Create and repair XFS filesystems
	sudo umount /dev/vgdata/lvdata
	sudo mkfs -t xfs -f /dev/vgdata/lvdata
	sudo xfs_repair /dev/vgdata/lvdata



# MDRAID or DMRAID



# Create RAID 5 using LVM
	sudo umount /dev/vgdata/lvdata
	sudo vgremove vgdata
	lsblk
	sudo vgcreate vgraid /dev/sdb1 /dev/sdc1 /dev/sdd1
	sudo pvs
	sudo lvcreate --type raid5 -i 2 -l 100%VG -n lvraid vgraid
	sudo lvs
	sudo mkfs -t ext4 /dev/vgraid/lvraid
	sudo mkdir /media/lvraid
	sudo mount /dev/vgraid/lvraid /media/lvraid
	df -h



# Creating RAIDs using mdadm
	sudo umount /media/lvraid
	sudo vgremove vgraid
	lsblk
	sudo pvremove /dev/sdb1
	sudo pvremove /dev/sdc1
	sudo pvremove /dev/sdd1
	sudo gdisk /dev/sdb1
	sudo gdisk /dev/sdc1
	sudo gdisk /dev/sdd1
	sudo gdisk /dev/sde
	sudo mdadm --create /dev/md/mdraid /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1 --level=5 --raid-devices=4 --bimap=internal
	lsblk
	cat /proc/mdstat
	sudo mdadm --detail -scan
	sudo systemctl enable mdmonitor
	sudo systemctl start mdmonitor
	sudo mkfs -t xfs /dev/md/mdraid
	sudo mkdir /mnt/mdraid
	sudo mount /dev/md/mdraid /mnt/mdraid
	lsblk




# Linux software management systemps
	dpkg
	rpm

	apt
	yum
	zypper
	urpmi



# Get package information with RPM
	rpm -qa
	rpm -qi bash
	rpm -qa Group="System Environment/Shells"
	rpm -qa --last
	rpm -ql yum
	rpm -qd yum
	rpm -qc yum
	rpm -qf /bin/bash
	rpm -qdf /bin/bash
	rpm -q --provides bash
	rpm -q --requires bash
	rpm -q --changelog bash

	mkdir /tmp/packages
	sudo yum install -y yum-plugin-downloadonly
	sudo yum install --downloadonly --downloaddir=/tmp/packages httpd



# Manage software with YUM
	sudo yum -y install tree
	yum list updates
	sudo yum upgrade xz
	sudo yum remove whois
	sudo yum install -y wireshark
	sudo yum remove wireshark
	sudo yum autoremove wireshark
	sudo yum install -y yum-utils
	package-cleanup --leaves




# Manage sofware groups with YUM
	yum group list ids
	sudo yum group install security-tools
	sudo yum group remove security-tools
	sudo yum autoremove @security-tools
	sudo yum group update development
	yum group list
	sudo yum group install system-admin-tools
	yum group info system-admin-tools
	sudo yum group install system-admin-tools --setopt=group_package_types=mandatory,default,optional



# Work with YUM repositories
	ls /etc/yum.repos.d/
	yum repolist
	yum repolist enabled
	yum repolist disabled
	yum repoinfo




# Get package information with dpkg and APT
	dpkg
	apt
		apt-get
		apt-cache
		apt

	apt search 
	apt show
	apt list

	sudo apt search whois
	sudo apt info whois
	sudo apt list --installed whois
	sudo apt list whois
	sudo apt install whois

	sudo apt info apache
	apt-get download whois
	sudo dpkg --info whois_5.4.2_amd64.deb
	sudo dpkg -c whois_5.4.2_amd64.deb
	sudo dpkg -i whois_5.4.2_amd64.deb
	sudo dpkg -L whois
	sudo dpkg -S /usr/bin/whois




# Manage software with dpkg and APT
	sudo apt install apache2
	sudo apt info apache2
	sudo apt list --upgradable
	sudo apt update
	sudo apt upgrade vim-common
	sudo apt remove apache2
	sudo apt autoremove
	sudo dpkg -r whois
	sudo apt purge apache2
	sudo apt full-upgrade




# Work with APT repositories
	apt policy
	grep '^deb' /etc/apt/sources.list /etc/apt/soruces.list.d/*
	sudo add-apt-repository 'deb http://archive.canonical.com/ubuntu/ disco partner'
	sudo add-apt-repository --remove 'deb http://archive.canonical.com/ubuntu/ disco partner'
	sudo add-apt-repository ppa:libreoffice/ppa




# Install sofware from source
	sudo yum info git
	sudo yum group install -y "Development tools"
	sudo yum install -y gettext-devel openssl-devel perl-CPAN perl-devel zlib-devel
	wget URLLINK
	tar -xzvpf FILE
	cd FILE
	make configure
	./configure --prefix=/user/local
	sudo make install



# Create and delete local user accounts
	sudo useradd bob
	sudo passwd bob
	sudo chage -l bob
	sudo userdel -r bob



# Modify local user accounts
	sudo useradd sally
	sudo passwd sally
	sudo usermod -a -G audio sally
	sudo usermod -L sally
	sudo usermod -U sally
	sudo usermod -s /sbin/nologin sally
	sudo usermod -s /sbin/bash sally



# Locale and date tools
	localectl
	localectl list-locales
	localectl set-locale LANG=en_us.utf8
	localectl list-keymaps
	localectl set-keymap us
	date --utc
	date +"%h %d %Y"
	date +"%s"
	date --date '+10 days'
	date --date 'next thursday'
	cal
	cal -3
	cal 3
	cal 1752




# Get systemd service status
	systemctl list-unit-files -at service
	systemctl list-units -at service
	systemctl list-units -t service --state running
	systemctl cat rsyslog
	systemctl status rsyslog



# Manage systemd services
	systemctl list-unit-files -at service
	sudo systemctl stop atd
	systemctl status atd
	sudo systemctl start atd
	sudo systemctl restart atd
	systemctl is-active atd
	sudo systemctl mask atd
	sudo systemctl start atd
	sudo systemctl unmask atd



# Make systemd service persistent
	systemctl list-unit-files -at service
	sudo systemctl disable atd
	systemctl is-enabled atd
	sudo systemctl enable atd
	systemctl status atd



# Manage sysvinit services
		1. kernel runs /sbin/init
		2. init loads /etc/inittab
		3. inittab configures...
			default runlevel
			ctrl alt del
			executes links in /etc/rc.d/rc.<runlevel>
		4. runlevel scripts start services

	runlevel 0 - halt
	runlevel 1 - single user mode
	runlevel 2 - multi-user mode
	runlevel 3 - multi-user mode with networking
	runlevel 4 - undefined
	runlevel 5 - multi-user mode gui
	runlevel 6 - reboot

	cat /etc/inittab

	chkconfig --list
	chkconfig --add httpd
	chkconfig --del httpd
	chkconfig --level 345 httpd on

	service httpd start
	service httpd stop
	service httpd restart
	service httpd reload
	service httpd status
	service --status-all
	/etc/init.d/httpd start

	init 3




# Manage one-time jobs with AT
	sudo yum install -y at
	sudo systemctl start atd
	sudo systemctl enable atd
	at now +5min
	at> mkdir ~/Document.bak
	at> rsync -a ~/Documents/ ~/Documents.bak

	atq
	at -c 1
	atrm 1
	atq

	batch
	at> touch ~/batchfile.txt
	atq




# Manage reoccuring user jobs cron
	sudo yum install -y cronie crontabs
	sudo systemctl start crond
	sudo systemctl enable crond
	crontab -e
	crontab -l
	crontab -r



# Manage reoccuring system jobs with cron
	sudo vi /etc/cron.d/backupdocs
	ls -l /etc/cron/*
	man crontab
	man 5 crontab


# Understand file system paths
	sudo yum install tree
	tree /etc
	file /etc/services
	
	
	
# Create files and dirs
	vim ~/textfile.txt
	echo "test" >> ~/textfile.txt
	mkdir ~/newdir
	mkdir -p ~/parent/child
	mkdir ~/{dir1,dir2,dir3}
	ls -d ~/dir?


	

# Find files using Locate
	locate bzip2
	locate -c bzip2
	locate bzip2 man
	locate -c bzip2 man
	locate -A bzip2 man
	locate -i high
	locate --regexp '^/usr.*pixmaps.*jpg$'
	locate --regex '^/usr.*(pixmaps|backgrounds).*jpg'
	locate -S
	sudo updatedb
	
	
	
# Finding files with Find
	sudo find / -name bzip2
	sudo find / -name *bzip2* | sort
	sudo find / -user grant
	sudo find / -group grant
	sudo find / -size +50k
	sudo find / -size +1M -exec stat -c "%s %n" {} \;
	sudo find / -size +1M -exec stat -c "%s %n" {} \; | sort -n
	sudo find / -mtime -1
	sudo find / -mtime +1
	sudo find /home -type f
	sudo find /home -type d
	
	
	
# Archive files
	sudo tar --xattrs -cvpf etc.tar /etc
	sudo tar --xattrs --gzip -vcfp etc.tar.gz /etc
	sudo tar --xattrs --bzip2 -vcfp etc.tar.bz2 /etc
	sudo tar --xattrs --xz -vcfp etc.tar.xz /etc
	tar -tf etc.tar
	tar -tf etc.tar | grep firewall
	sudo tar --xattrs -xvpf etc.tar
	sudo tar --xattrs -xvpf etc.tar -C /home/user1/Downloads
	
	
	

# Compress files
	gzip services
	gunzip services.gz
	bzip2 services
	bunzip2 services.bz2
	xz services
	unxz services.xz
	zip services.zip services
	unzip services.zip




# User booleans to modify SELinux behavior
	getsebool -a
	getsebool mozilla_plugin_use_gps
	sestatus -b
	sudo semanage boolean -l
	sudo setsebool mozilla_plugin_use_gps on
	sudo setsebool -P mozilla_plugin_use_gps on
	sudo semanage boolean -l | egrep 'SElinux|mozilla_plugin_use_gps'
	
	
	

# Diagnose routing SELinux policy violations
	sudo chcon -t etc_t /etc/shadow
	sudo ls -lZ /etc/shadow
	sudo ausearch -m avc
	sudo restorecon /etc/shadow
	
	
	

#Configure access and authentication using PAM
	ldd /bin/su
	
	

# Using pam_tally2
	sudo vi /etc/pam.d/password-auth
		auth required pam_tally2 deny=5 unlock_time=60
	sudo vi /etc/pam.d/system-auth
		auth required pam_tally2 deny=5 unlock_time=60
	sudo pam_tally2 -u user1
	sudo pam_tally2 -r -u user1
	



	
# Change default password policies
	cat /etc/security/pwquality.conf
	sudo passwd --expire grant
	sudo chage -d 0 grant
	
	


	
# Lock user accounts and change password aging
	chage -d 0
	chage --lastday 2017-10-25
	chage --expiredate 2017-10-25
	chage --inactive 7
	chage --mindays 0
	chage --maxdays 99999
	chage --warndays 7
	chage --list grant
	


	
# Implementing SSH keypairs for authentication
	ssh-keygen
	ssh-copy-id user1@192.168.0.21
	ssh-add
	
	

	
# Deny access using TCP Wrappers
	/etc/hosts.allow
	/etc/hosts.deny




# Restrict access to TTY consoles
	/etc/securetty





# Compare and contrast Linux firewalls
	iptables -A INPUT -p tcp --dport 80 -j ACCEPT
	
	firewall-cmd --zone=public --permanent --add-service=ssh
	iptables -A INPUT -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
	
	
	


# User firewalld for packet filtering
	sudo systemctl start firewalld
	sudo systemctl enable firewalld
	sudo firewall-cmd --state
	sudo firewall-cmd --timeout=60
	sudo firewall-cmd --permanent
	sudo firewall-cmd --permanent --add-service=http
	sudo firewall-cmd --permanent --remove-service=http
	sudo firewall-cmd --permanent --add-port=443/tcp
	sudo firewall-cmd --permanent --add-port=5901-5910/tcp
	sudo firewall-cmd --reload
	sudo firewall-cmd --get-services
	sudo firewall-cmd --list-services
	sudo firewall-cmd --list-ports
	
	
	
	
# Use firewalld zones
	sudo firewall-cmd --get-default-zone
	sudo firewall-cmd --list-all-zones
	sudo firewall-cmd --permananet --new-zone=coffeeshop
	sudo firewall-cmd --permanent --add-source=10.10.10.0/24
	sudo firewall-cmd --permanent --add-service=http
	sudo firewall-cmd --reload
	sudo firewall-cmd --set-default-zone=coffeshop
	sudo firewall-cmd --get-default-zone
	sudo firewall-cmd --reload
	sudo firewall-cmd --list-all --zone=coffeshop
	
	
	
	
# Use firewalld for NAT
	sudo firewall-cmd --permanent --zone=coffeshop --add-masquerade
	sudo firewall-cmd --permanent --query-masquerade
	sudo firewall-cmd --permanent --zone?coffeeshop --add-rich-rule='rule familu=ipv3 source address=192.168.100.0/24 masquerade'
	sudo firewall-cmd --permanent --zone=coffeeshop --add-forward-port=22:proto=tcp:toport=2222:toaddr=192.168.100.101
	sudo firewall-cmd --permanent --list-all --zone=coffeeshop




# Create dynamic rules
	sudo yum install -y epel-release
	sudo yum install -y fail2ban fail2ban-systemd
	sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
	sudo vi /etc/fail2ban/jail.local
	sudo systemctl start firewalld
	sudo systemctl enable firewalld
	sudo systemctl start fail2ban
	sudo systemctl enable fail2ban
	sudo fail2ban-client status




# Locate and interpret system log files
	sudo systemctl start rsyslog
	sudo systemctl enable rsyslog
	less -N /etc/rsyslog.conf
	
	
	
# Reading the system journal
	sudo journalctl
	sudo journalctl -k
	sudo journalctl /sbin/crond
	sudo journalctl -u crond
	sudo journalctl -f
	sudo mkdir -p /var/log/journal
	sudo systemctl restart systemd-journald
	journalctl --since yestarday
	
	
	
# Rsyslog filters
	sudo less -N /etc/logrotate.conf
	ls /etc/logrotate.d
	
	
	
# Rsyslog actions
	cron.* /var/log/cron.log
	cron.* -/var/log/cron.log



# Archiving files with tar and cpio
	sudo tar --xattrs -cvpf etc.tar /etc
	sudo tar --xattrs --gzip -cvpf etc.tar.gz /etc
	sudo tar --xattrs --bzip2 -cvpf etc.tar.bz2 /etc
	sudo tar --xatrrs --xz -cvpf etc.tar.xz /etc
	sudo tar --xattrs -xvpf etc.tar
	sudo tar --xattrs -xvpf etc.tar -C /home/user1/Downloads
	
	
	

