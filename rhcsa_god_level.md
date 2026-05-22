# 🐧 RHCSA God-Level Linux Reference

> **RHCSA (Red Hat Certified System Administrator)** — EX200 Exam ke liye complete command aur concept reference.
> Platform: RHEL 8/9 | Bash | Systemd

---

## 📋 Table of Contents

1. [Essential Tools & File System Navigation](#1-essential-tools--file-system-navigation)
2. [User & Group Management](#2-user--group-management)
3. [File Permissions & Ownership](#3-file-permissions--ownership)
4. [Special Permissions (SUID, SGID, Sticky Bit)](#4-special-permissions-suid-sgid-sticky-bit)
5. [Storage Management (Partitions, LVM)](#5-storage-management-partitions-lvm)
6. [File Systems & Mounting](#6-file-systems--mounting)
7. [Networking](#7-networking)
8. [Process Management](#8-process-management)
9. [Systemd & Services](#9-systemd--services)
10. [Scheduling (cron, at)](#10-scheduling-cron-at)
11. [Package Management (DNF/YUM)](#11-package-management-dnfyum)
12. [Logging & Journald](#12-logging--journald)
13. [SSH & Remote Access](#13-ssh--remote-access)
14. [Firewall (firewalld)](#14-firewall-firewalld)
15. [SELinux — God Mode 🔐](#15-selinux--god-mode-)
16. [Containers (Podman)](#16-containers-podman)
17. [Boot Process & GRUB2](#17-boot-process--grub2)
18. [Kernel Parameters & Tuning (tuned)](#18-kernel-parameters--tuning-tuned)
19. [Archiving & Compression](#19-archiving--compression)
20. [Text Processing (grep, sed, awk)](#20-text-processing-grep-sed-awk)
21. [Links (Hard & Symbolic)](#21-links-hard--symbolic)
22. [Find & Locate](#22-find--locate)
23. [sudo & Privilege Escalation](#23-sudo--privilege-escalation)
24. [Time & NTP (chrony)](#24-time--ntp-chrony)
25. [Swap Space](#25-swap-space)
26. [Stratis & VDO (RHEL 8)](#26-stratis--vdo-rhel-8)
27. [AutoFS (Automounting)](#27-autofs-automounting)
28. [NFS & Samba (Basic)](#28-nfs--samba-basic)
29. [Shell Scripting Basics](#29-shell-scripting-basics)
30. [Exam Tips & Tricks 🎯](#30-exam-tips--tricks-)

---

## 1. Essential Tools & File System Navigation

```bash
# File system hierarchy
/           # Root
/etc        # Configuration files
/var        # Variable data (logs, spool)
/tmp        # Temporary files
/home       # User home directories
/root       # Root user home
/boot       # Bootloader & kernel
/dev        # Device files
/proc       # Process/kernel virtual FS
/sys        # Kernel hardware info
/usr        # User binaries & libraries
/opt        # Optional/third-party software
/mnt        # Manual mount point
/media      # Removable media
/run        # Runtime data (since boot)

# Navigation
pwd                     # Current directory
ls -la                  # List all with permissions
ls -lhS                 # Sort by size, human-readable
cd -                    # Previous directory
tree /etc               # Directory tree view

# File operations
cp -r src/ dest/        # Recursive copy
mv oldname newname      # Move/rename
rm -rf dir/             # Force delete recursive
mkdir -p /a/b/c         # Create nested dirs
touch file.txt          # Create/update timestamp

# Viewing files
cat /etc/passwd
less /var/log/messages
head -20 file.txt
tail -f /var/log/secure  # Live follow log

# Output redirection
command > file.txt       # Overwrite stdout
command >> file.txt      # Append stdout
command 2> err.txt       # Redirect stderr
command &> all.txt       # Both stdout + stderr
command 2>&1 | less      # Pipe stderr+stdout

# Pipes & chaining
ls -la | grep "^d"       # List only dirs
cat file | sort | uniq   # Sort + deduplicate
command1 && command2     # Run cmd2 only if cmd1 succeeds
command1 || command2     # Run cmd2 only if cmd1 fails
```

---

## 2. User & Group Management

```bash
# Users
useradd username                    # Create user
useradd -m -s /bin/bash -G wheel user1   # Full create
useradd -u 1500 -d /data/user1 user1    # Custom UID & home
usermod -aG groupname username      # Add to group (append)
usermod -s /sbin/nologin username   # Disable login
usermod -L username                 # Lock account
usermod -U username                 # Unlock account
userdel -r username                 # Delete user + home
passwd username                     # Set password
passwd -e username                  # Force password change at next login
chage -l username                   # Show password aging
chage -M 90 -m 7 -W 14 username     # Max 90d, min 7d, warn 14d
chage -E 2025-12-31 username        # Account expiry

# /etc/passwd format
# username:x:UID:GID:comment:home:shell
# x = password in /etc/shadow

# /etc/shadow format
# username:$6$hash:lastchange:min:max:warn:inactive:expire

# Groups
groupadd groupname
groupadd -g 2000 devops
groupmod -n newname oldname
groupdel groupname
groups username                     # Show user's groups
id username                         # UID, GID, groups

# Switching users
su - username                       # Switch with full env
su -c "command" username            # Run single command as user
```

---

## 3. File Permissions & Ownership

```bash
# Permission structure: rwxrwxrwx (owner|group|others)
# r=4, w=2, x=1

# chmod
chmod 755 file          # rwxr-xr-x
chmod 644 file          # rw-r--r--
chmod u+x file          # Add execute for owner
chmod g-w file          # Remove write for group
chmod o=r file          # Set others to read-only
chmod -R 750 /dir       # Recursive

# chown
chown user:group file
chown -R user:group /dir
chown :group file       # Change only group
chgrp group file        # Change group only

# umask
umask                   # Show current (default 0022)
umask 0027              # Files=640, Dirs=750
# umask value is subtracted from 666 (files) / 777 (dirs)

# Default permissions:
# File: 666 - 022 = 644
# Dir:  777 - 022 = 755
```

---

## 4. Special Permissions (SUID, SGID, Sticky Bit)

```bash
# SUID (Set User ID) — file runs as owner
chmod u+s /path/to/binary
chmod 4755 file
# Example: /usr/bin/passwd (runs as root)
find / -perm -4000 -type f 2>/dev/null   # Find all SUID files

# SGID (Set Group ID)
# On file: runs as group owner
# On directory: new files inherit group
chmod g+s /shared/dir
chmod 2775 /shared/dir
find / -perm -2000 -type f 2>/dev/null

# Sticky Bit — only owner can delete files in directory
chmod +t /tmp
chmod 1777 /tmp
# Example: /tmp (anyone can create, only owner can delete)
find / -perm -1000 -type d 2>/dev/null

# ls output indicator:
# -rwsr-xr-x  → SUID set (s in owner execute)
# -rwxr-sr-x  → SGID set (s in group execute)
# drwxrwxrwt  → Sticky bit (t in others execute)
```

---

## 5. Storage Management (Partitions, LVM)

### Disk Partitioning

```bash
lsblk                          # List block devices
fdisk -l                       # List all partitions
fdisk /dev/sdb                 # MBR partition (interactive)
  # n = new, p = primary, w = write
gdisk /dev/sdb                 # GPT partition
parted /dev/sdb print          # Show partition table
parted /dev/sdb mklabel gpt    # Create GPT label
parted /dev/sdb mkpart primary ext4 1MiB 10GiB

# After partitioning
partprobe /dev/sdb             # Inform kernel of changes
```

### LVM (Logical Volume Manager) — RHCSA CORE

```bash
# Step 1: Physical Volume (PV)
pvcreate /dev/sdb1 /dev/sdc1
pvs                            # Show PVs
pvdisplay /dev/sdb1

# Step 2: Volume Group (VG)
vgcreate vg_data /dev/sdb1 /dev/sdc1
vgs
vgdisplay vg_data
vgextend vg_data /dev/sdd1    # Add PV to VG
vgreduce vg_data /dev/sdc1    # Remove PV from VG

# Step 3: Logical Volume (LV)
lvcreate -L 5G -n lv_home vg_data       # Fixed size
lvcreate -l 100%FREE -n lv_data vg_data # Use all free
lvs
lvdisplay /dev/vg_data/lv_home

# Format & Mount
mkfs.xfs /dev/vg_data/lv_home
mount /dev/vg_data/lv_home /home

# Extend LV
lvextend -L +2G /dev/vg_data/lv_home
lvextend -l +50%FREE /dev/vg_data/lv_home
xfs_growfs /home               # XFS resize (online)
resize2fs /dev/vg_data/lv_ext4 # ext4 resize

# Reduce LV (ext4 only — XFS CANNOT be shrunk)
umount /dev/vg_data/lv_ext4
e2fsck -f /dev/vg_data/lv_ext4
resize2fs /dev/vg_data/lv_ext4 3G
lvreduce -L 3G /dev/vg_data/lv_ext4
mount /dev/vg_data/lv_ext4 /mountpoint

# Remove LVM
lvremove /dev/vg_data/lv_home
vgremove vg_data
pvremove /dev/sdb1
```

---

## 6. File Systems & Mounting

```bash
# Create file systems
mkfs.xfs /dev/sdb1             # XFS (default in RHEL)
mkfs.ext4 /dev/sdb1            # ext4
mkfs.vfat /dev/sdb1            # FAT32

# Mounting
mount /dev/sdb1 /mnt/data
mount -o ro /dev/sdb1 /mnt     # Read-only
mount -o remount,rw /mnt       # Remount read-write
umount /mnt/data
umount -l /mnt/data            # Lazy unmount

# Check disk usage
df -hT                         # Disk usage + fs type
du -sh /home/*                 # Dir sizes
du -sh --max-depth=1 /var

# /etc/fstab — Persistent mounts
# <device>  <mountpoint>  <fstype>  <options>  <dump>  <pass>
UUID=xxxx   /home   xfs   defaults  0  2
/dev/vg_data/lv_home  /home  xfs  defaults  0  0

# Get UUID
blkid /dev/sdb1
blkid -s UUID -o value /dev/sdb1

# Validate fstab
mount -a            # Mount all from fstab (test)
findmnt             # Show mount tree
findmnt --verify    # Verify fstab entries

# XFS tools
xfs_info /home      # XFS filesystem info
xfs_repair /dev/sdb1  # Repair XFS (unmounted)

# ext4 tools
tune2fs -l /dev/sdb1          # Show ext4 info
e2fsck -f /dev/sdb1           # Force check
```

---

## 7. Networking

```bash
# Network info
ip addr show                    # All interfaces + IPs
ip addr show eth0               # Specific interface
ip link show                    # Link layer info
ip route show                   # Routing table
ip route add default via 192.168.1.1   # Add default route
ss -tuln                        # Open ports (like netstat)
ss -tulnp                       # With process info
nmcli device status             # NetworkManager devices

# NetworkManager (nmcli) — RHCSA KEY TOOL
nmcli con show                  # All connections
nmcli con show --active         # Active only
nmcli device show eth0          # Interface details

# Create/Modify connection
nmcli con add type ethernet ifname eth0 con-name myconn \
  ipv4.addresses 192.168.1.10/24 \
  ipv4.gateway 192.168.1.1 \
  ipv4.dns 8.8.8.8 \
  ipv4.method manual

nmcli con mod myconn ipv4.addresses 192.168.1.20/24
nmcli con mod myconn +ipv4.dns 1.1.1.1   # Add DNS
nmcli con up myconn
nmcli con down myconn
nmcli con delete myconn

# DHCP
nmcli con mod myconn ipv4.method auto

# Hostname
hostnamectl set-hostname server01.example.com
hostnamectl status
cat /etc/hostname

# /etc/hosts
echo "192.168.1.50 db.lab.local" >> /etc/hosts

# DNS resolution order
cat /etc/nsswitch.conf    # hosts: files dns myhostname
cat /etc/resolv.conf      # nameserver entries

# Diagnostics
ping -c 4 8.8.8.8
traceroute 8.8.8.8
nslookup hostname
dig hostname
curl -I https://example.com
wget -O /dev/null https://example.com

# Network config files (RHEL 8/9)
ls /etc/NetworkManager/system-connections/
```

---

## 8. Process Management

```bash
# View processes
ps aux                          # All processes (BSD style)
ps -ef                          # All processes (SysV style)
ps aux | grep nginx
top                             # Interactive process viewer
htop                            # Enhanced top (if installed)

# Process signals
kill -l                         # List all signals
kill PID                        # SIGTERM (graceful)
kill -9 PID                     # SIGKILL (force)
kill -HUP PID                   # SIGHUP (reload config)
killall nginx                   # Kill by name
pkill -u username               # Kill all procs of user

# Process priority (nice)
nice -n 10 command              # Start with nice=10 (lower priority)
renice -n 5 -p PID              # Change existing process priority
# Nice range: -20 (highest priority) to 19 (lowest)
# Only root can set negative nice values

# Background jobs
command &                       # Run in background
jobs                            # List background jobs
fg %1                           # Bring job 1 to foreground
bg %1                           # Continue job 1 in background
Ctrl+Z                          # Suspend current job
nohup command &                 # Run even after logout
disown %1                       # Detach from shell

# Signals
Ctrl+C  = SIGINT  (terminate)
Ctrl+Z  = SIGTSTP (suspend)
Ctrl+D  = EOF

# lsof
lsof /path/to/file              # Who has this file open
lsof -i :80                     # What's using port 80
lsof -u username                # All files opened by user
```

---

## 9. Systemd & Services

```bash
# Service management
systemctl start nginx
systemctl stop nginx
systemctl restart nginx
systemctl reload nginx          # Reload config without restart
systemctl status nginx
systemctl enable nginx          # Enable at boot
systemctl disable nginx
systemctl enable --now nginx    # Enable + start immediately
systemctl is-active nginx
systemctl is-enabled nginx
systemctl is-failed nginx
systemctl mask nginx            # Completely disable (unmaskable)
systemctl unmask nginx

# List services
systemctl list-units --type=service
systemctl list-units --type=service --state=running
systemctl list-unit-files --type=service
systemctl list-units --failed

# Systemd targets (runlevels)
systemctl get-default           # Current default target
systemctl set-default multi-user.target   # Non-graphical
systemctl set-default graphical.target    # Graphical
systemctl isolate rescue.target  # Switch to rescue mode

# Target equivalents:
# runlevel 0 = poweroff.target
# runlevel 1 = rescue.target
# runlevel 3 = multi-user.target
# runlevel 5 = graphical.target
# runlevel 6 = reboot.target

# Analyze boot
systemd-analyze
systemd-analyze blame           # Slowest services
systemd-analyze critical-chain  # Boot dependency chain

# Unit file locations
/usr/lib/systemd/system/        # Default (don't edit)
/etc/systemd/system/            # Custom (override here)

# Create custom service
cat > /etc/systemd/system/myapp.service << 'EOF'
[Unit]
Description=My Application
After=network.target

[Service]
Type=simple
User=appuser
ExecStart=/opt/myapp/start.sh
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload         # After creating/editing unit files
systemctl enable --now myapp
```

---

## 10. Scheduling (cron, at)

```bash
# cron — recurring jobs
crontab -e          # Edit current user's crontab
crontab -l          # List current user's crontab
crontab -r          # Remove crontab
crontab -u user -e  # Edit another user's crontab (root)

# Crontab format:
# MIN HOUR DAY MONTH WEEKDAY command
# * = every, */5 = every 5, 1-5 = range, 1,3,5 = list

# Examples
0 2 * * *    /opt/backup.sh          # 2:00 AM daily
*/15 * * * * /opt/check.sh           # Every 15 minutes
0 9 * * 1-5  /opt/report.sh          # 9 AM Mon-Fri
0 0 1 * *    /opt/monthly.sh         # Midnight, 1st of month
@reboot      /opt/startup.sh         # At every boot
@daily       /opt/daily.sh           # Once per day

# System cron directories
/etc/cron.hourly/
/etc/cron.daily/
/etc/cron.weekly/
/etc/cron.monthly/
/etc/crontab                         # System crontab

# Access control
/etc/cron.allow   # Only listed users can use cron
/etc/cron.deny    # Listed users cannot use cron

# at — one-time jobs
at 14:30                    # Prompt for commands
at now + 1 hour             # 1 hour from now
at midnight                 # Tonight at midnight
at 9am tomorrow
atq                         # List pending jobs
atrm 3                      # Remove job #3
at -l                       # Same as atq
echo "/opt/script.sh" | at now + 5 minutes

# systemd timers (modern alternative to cron)
systemctl list-timers       # Show all active timers
```

---

## 11. Package Management (DNF/YUM)

```bash
# Install / Remove
dnf install package-name
dnf install -y package1 package2
dnf remove package-name
dnf autoremove                  # Remove unused dependencies

# Update
dnf update                      # Update all
dnf update package-name         # Update specific
dnf upgrade                     # Same as update + obsoletes
dnf check-update                # Check for updates (no install)

# Search & Info
dnf search keyword
dnf info package-name
dnf list installed
dnf list available
dnf list installed | grep nginx
dnf provides /usr/bin/wget      # Which package owns this file
dnf whatprovides "*/semanage"   # Which package provides command

# Groups
dnf group list
dnf group install "Development Tools"
dnf group remove "Development Tools"
dnf group info "Server with GUI"

# History
dnf history
dnf history info 5
dnf history undo 5              # Undo transaction 5
dnf history redo 5

# Repos
dnf repolist
dnf repolist --all
dnf repolist enabled
dnf config-manager --enable repo-name
dnf config-manager --disable repo-name
dnf config-manager --add-repo URL

# Repo file location
ls /etc/yum.repos.d/

# Cache
dnf clean all
dnf makecache

# Module streams (RHEL 8+)
dnf module list
dnf module list php
dnf module enable php:8.0
dnf module install php:8.0
dnf module disable php:8.0
dnf module reset php

# Install from local RPM
dnf install ./package.rpm
rpm -ivh package.rpm            # Traditional RPM install
rpm -qa | grep httpd            # Query all installed
rpm -ql httpd                   # List files in package
rpm -qf /etc/httpd/conf/httpd.conf   # Which package owns file
rpm -qi httpd                   # Package info
rpm -qc httpd                   # Config files of package
rpm -e httpd                    # Remove package
rpm --verify httpd              # Verify package integrity
```

---

## 12. Logging & Journald

```bash
# journalctl — systemd journal
journalctl                      # All logs
journalctl -f                   # Live follow (like tail -f)
journalctl -n 50                # Last 50 lines
journalctl -u nginx             # Logs for specific service
journalctl -u nginx -f          # Follow service logs
journalctl --since "2024-01-01" --until "2024-01-31"
journalctl --since "1 hour ago"
journalctl --since today
journalctl -p err               # Only errors and above
journalctl -p warning           # Warning and above
journalctl -b                   # This boot only
journalctl -b -1                # Previous boot
journalctl -k                   # Kernel messages only
journalctl _UID=1000            # Logs for user UID 1000
journalctl --disk-usage
journalctl --vacuum-time=7d     # Keep only last 7 days

# Priority levels (--priority / -p):
# 0=emerg, 1=alert, 2=crit, 3=err, 4=warning, 5=notice, 6=info, 7=debug

# Persistent journal
mkdir -p /var/log/journal
systemd-tmpfiles --create --prefix /var/log/journal
# Edit /etc/systemd/journald.conf → Storage=persistent
systemctl restart systemd-journald

# rsyslog (traditional logging)
cat /etc/rsyslog.conf
systemctl status rsyslog

# Traditional log files
/var/log/messages       # General system messages
/var/log/secure         # Auth/security events (SSH, sudo)
/var/log/cron           # Cron job logs
/var/log/maillog        # Mail logs
/var/log/boot.log       # Boot process
/var/log/dnf.log        # Package manager
/var/log/audit/audit.log # SELinux + audit events

# logrotate
cat /etc/logrotate.conf
ls /etc/logrotate.d/
logrotate -f /etc/logrotate.conf  # Force rotation
```

---

## 13. SSH & Remote Access

```bash
# Connect
ssh user@hostname
ssh -p 2222 user@hostname          # Custom port
ssh -i ~/.ssh/id_rsa user@host     # Specific key
ssh -X user@host                   # X11 forwarding

# Key-based auth setup
ssh-keygen -t rsa -b 4096          # Generate key pair
ssh-keygen -t ed25519              # Modern (preferred)
ssh-copy-id user@hostname          # Copy public key to server
# OR manually:
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys

# SSH config (~/.ssh/config)
Host myserver
    HostName 192.168.1.100
    User admin
    Port 22
    IdentityFile ~/.ssh/id_rsa

# Server config (/etc/ssh/sshd_config)
PermitRootLogin no                 # Disable root SSH
PasswordAuthentication no         # Key-only auth
PubkeyAuthentication yes
Port 22
AllowUsers user1 user2
MaxAuthTries 3

systemctl restart sshd             # After config change

# SCP (Secure Copy)
scp file.txt user@host:/path/
scp user@host:/path/file.txt .
scp -r /local/dir user@host:/remote/

# SFTP
sftp user@host

# SSH tunneling
ssh -L 8080:localhost:80 user@host   # Local port forward
ssh -R 9090:localhost:3000 user@host # Remote port forward

# Known hosts
cat ~/.ssh/known_hosts
ssh-keyscan hostname >> ~/.ssh/known_hosts
```

---

## 14. Firewall (firewalld)

```bash
# firewalld — zone-based firewall
systemctl enable --now firewalld
systemctl status firewalld
firewall-cmd --state

# Zones
firewall-cmd --get-default-zone
firewall-cmd --set-default-zone=public
firewall-cmd --get-active-zones
firewall-cmd --list-all                    # Current zone details
firewall-cmd --list-all --zone=public

# Services
firewall-cmd --list-services
firewall-cmd --add-service=http            # Temporary (lost on reload)
firewall-cmd --add-service=http --permanent # Permanent
firewall-cmd --remove-service=http --permanent
firewall-cmd --get-services                # All available services

# Ports
firewall-cmd --add-port=8080/tcp --permanent
firewall-cmd --remove-port=8080/tcp --permanent
firewall-cmd --list-ports

# Reload (apply permanent rules)
firewall-cmd --reload

# Rich rules
firewall-cmd --add-rich-rule='rule family="ipv4" source address="192.168.1.0/24" service name="ssh" accept' --permanent

# Masquerading/NAT
firewall-cmd --add-masquerade --permanent

# Port forwarding
firewall-cmd --add-forward-port=port=80:proto=tcp:toport=8080 --permanent

# Emergency
firewall-cmd --panic-on            # Block ALL traffic
firewall-cmd --panic-off

# nftables (underlying)
nft list ruleset
```

---

## 15. SELinux — God Mode 🔐

SELinux (Security Enhanced Linux) — RHCSA mein sabse important aur darpok topic. Dar mat.

### Modes

```bash
getenforce                          # Current mode: Enforcing / Permissive / Disabled
setenforce 0                        # Set Permissive (temporary)
setenforce 1                        # Set Enforcing (temporary)

# Persistent mode in /etc/selinux/config
SELINUX=enforcing       # Fully enforce
SELINUX=permissive      # Log but don't block
SELINUX=disabled        # Disabled (needs reboot)

# Check config
cat /etc/selinux/config
sestatus                            # Full SELinux status
sestatus -v
```

### Contexts

```bash
# View contexts
ls -Z /var/www/html/               # File context
ps auxZ | grep httpd               # Process context
id -Z                              # Current user context

# Context format: user:role:type:level
# Example: system_u:object_r:httpd_sys_content_t:s0

# Change file context (temporary — gets reset)
chcon -t httpd_sys_content_t /mydata/index.html
chcon -R -t httpd_sys_content_t /mydata/

# Change context permanently (survives relabel)
semanage fcontext -a -t httpd_sys_content_t "/mydata(/.*)?"
restorecon -Rv /mydata/            # Apply the policy

# Restore default context
restorecon -v /var/www/html/
restorecon -Rv /etc/

# Common types:
# httpd_sys_content_t     — Apache web content
# ssh_home_t              — SSH authorized_keys
# var_t                   — General /var files
# etc_t                   — /etc config files
# usr_t                   — /usr files
```

### Booleans

```bash
# SELinux booleans — toggle specific behaviors
getsebool -a                        # All booleans
getsebool httpd_can_network_connect # Specific boolean
semanage boolean -l                 # With descriptions

setsebool httpd_can_network_connect on          # Temporary
setsebool -P httpd_can_network_connect on       # Permanent (-P flag!)
setsebool -P httpd_enable_homedirs on           # Allow Apache to serve ~/public_html

# Common booleans for exam:
# httpd_can_network_connect      — Apache can connect to network
# httpd_can_network_connect_db   — Apache to DB
# httpd_enable_homedirs          — Serve user home dirs
# samba_enable_home_dirs         — Samba access to home
# ftpd_anon_write                — Anonymous FTP write
# allow_ftpd_full_access         — Full FTP access
```

### Troubleshooting SELinux Denials

```bash
# View denials
ausearch -m avc -ts recent              # Recent denials
ausearch -m avc,user_avc -ts today      # Today's denials
ausearch -m avc -c httpd               # Specific process
grep "avc: denied" /var/log/audit/audit.log

# sealert — human-readable suggestions
sealert -a /var/log/audit/audit.log
dnf install setroubleshoot-server       # Install if missing
journalctl -t setroubleshoot            # via journald

# audit2allow — generate policy from denials
audit2allow -w -a                       # Explain denials
audit2allow -a -M mypolicy             # Create module
semodule -i mypolicy.pp                # Install module
semodule -l                            # List modules
semodule -r mypolicy                   # Remove module

# audit2why — explain why something was denied
audit2why -a

# Typical SELinux exam scenario:
# 1. Service not working → check getenforce
# 2. If permissive works but enforcing fails → SELinux issue
# 3. Check: wrong context? → restorecon
# 4. Check: boolean needed? → setsebool -P
# 5. Check: non-standard port? → semanage port
```

### SELinux Ports

```bash
# View ports allowed for a service
semanage port -l | grep http
semanage port -l | grep ssh

# Add non-standard port
semanage port -a -t http_port_t -p tcp 8888
semanage port -a -t ssh_port_t -p tcp 2222

# Modify existing
semanage port -m -t http_port_t -p tcp 8888

# Delete
semanage port -d -t http_port_t -p tcp 8888

# Common port types:
# http_port_t     — 80, 443, 8080, 8443...
# ssh_port_t      — 22
# mysqld_port_t   — 3306
# ftp_port_t      — 21
```

### SELinux Users & Roles

```bash
semanage user -l                    # SELinux user mappings
semanage login -l                   # Linux user to SELinux user mapping
semanage login -a -s staff_u username
id -Z                               # Show current SELinux user:role:type

# SELinux users:
# unconfined_u  — No restrictions (default for regular users)
# staff_u       — Limited admin
# sysadm_u      — System admin
# system_u      — System processes
```

---

## 16. Containers (Podman)

```bash
# Podman — rootless Docker alternative (RHEL default)
podman --version
podman info

# Images
podman search nginx
podman pull nginx
podman images
podman rmi nginx                    # Remove image
podman image inspect nginx

# Containers
podman run -d -p 8080:80 --name webserver nginx
podman run -it --rm ubi8 /bin/bash  # Interactive, remove on exit
podman run -v /mydata:/data:Z nginx  # Volume with SELinux :Z label!
podman ps                           # Running containers
podman ps -a                        # All containers
podman stop webserver
podman start webserver
podman restart webserver
podman rm webserver
podman exec -it webserver /bin/bash
podman logs webserver
podman logs -f webserver            # Follow logs
podman inspect webserver

# Volumes
podman volume create mydata
podman volume ls
podman volume inspect mydata
podman volume rm mydata

# Container as systemd service (RHCSA 9 topic)
podman generate systemd --name webserver --files --new
# Copy generated file to ~/.config/systemd/user/
mkdir -p ~/.config/systemd/user
podman generate systemd --name webserver > ~/.config/systemd/user/container-webserver.service
systemctl --user enable --now container-webserver
loginctl enable-linger username     # Enable service without being logged in

# Rootless containers
podman run -d nginx                 # Runs as current user
podman unshare cat /etc/subuid      # UID mapping

# Podman networking
podman network ls
podman network create mynet
podman run --network mynet nginx

# Build images
podman build -t myimage:latest .    # From Dockerfile/Containerfile
podman tag myimage registry/user/myimage:v1
podman push registry/user/myimage:v1
podman login registry.redhat.io

# RHCSA container SELinux tip:
# Always use :Z (relabel for container) or :z (shared) with volumes
podman run -v /hostdata:/data:Z nginx
```

---

## 17. Boot Process & GRUB2

```bash
# Boot sequence:
# BIOS/UEFI → GRUB2 → Kernel → initramfs → systemd → default.target

# GRUB2 config
cat /boot/grub2/grub.cfg                # Generated config (don't edit)
cat /etc/default/grub                   # Edit this file
ls /etc/grub.d/                         # Scripts

# After editing /etc/default/grub:
grub2-mkconfig -o /boot/grub2/grub.cfg          # BIOS
grub2-mkconfig -o /boot/efi/EFI/redhat/grub.cfg # UEFI

# Common GRUB settings
GRUB_TIMEOUT=5
GRUB_DEFAULT=0
GRUB_CMDLINE_LINUX="crashkernel=auto rhgb quiet"

# At boot — edit GRUB entry:
# 1. Press 'e' on boot entry
# 2. Find 'linux' line
# 3. Edit kernel parameters
# 4. Ctrl+X to boot

# Reset root password (EXAM CRITICAL!)
# 1. At GRUB → press 'e'
# 2. Find linux line, add: rd.break at the end
# 3. Ctrl+X to boot
# In initramfs shell:
mount -o remount,rw /sysroot
chroot /sysroot
passwd root                         # Set new password
touch /.autorelabel                 # Fix SELinux contexts!
exit
exit                                # System reboots

# Single user mode
# Add 'single' or 'init=/bin/bash' or '1' to kernel line

# Boot to specific target
# Add to kernel line: systemd.unit=rescue.target
#                 or: systemd.unit=emergency.target

# Emergency mode (read-only root)
systemctl isolate emergency.target

# Check boot messages
dmesg
dmesg | grep -i error
dmesg | grep -i fail
journalctl -b                       # This boot
journalctl -b -1                    # Previous boot
```

---

## 18. Kernel Parameters & Tuning (tuned)

```bash
# sysctl — runtime kernel parameters
sysctl -a                               # All parameters
sysctl vm.swappiness                    # Show specific
sysctl -w vm.swappiness=10             # Set temporarily
sysctl -w net.ipv4.ip_forward=1        # Enable IP forwarding

# Persistent via file
echo "vm.swappiness=10" > /etc/sysctl.d/99-custom.conf
sysctl -p /etc/sysctl.d/99-custom.conf  # Apply
sysctl --system                         # Apply all

# Common parameters
vm.swappiness=10              # Prefer RAM over swap (0-100)
net.ipv4.ip_forward=1        # Enable routing
net.core.somaxconn=1024      # Max socket connections
vm.dirty_ratio=15            # % RAM for dirty pages

# tuned — performance profiles
tuned-adm list                # Available profiles
tuned-adm active              # Current profile
tuned-adm profile balanced    # Set profile
tuned-adm profile throughput-performance   # For servers
tuned-adm profile virtual-guest            # For VMs
tuned-adm profile powersave               # For laptops
tuned-adm recommend           # Recommended profile
tuned-adm off                 # Disable tuned

# Common profiles:
# balanced          — Good balance of power/performance
# throughput-performance — Max network/disk throughput
# latency-performance    — Low latency
# network-latency        — Low latency for network
# virtual-guest     — Optimized for VMs
# desktop           — Responsive desktop

# Resource limits (ulimit)
ulimit -a                     # All limits for current user
ulimit -n 65536               # Max open files (temporary)

# Persistent limits
cat /etc/security/limits.conf
cat /etc/security/limits.d/20-nproc.conf
# Format: <user> <soft/hard> <type> <value>
# *    soft    nofile    65536
# *    hard    nofile    65536
```

---

## 19. Archiving & Compression

```bash
# tar — Tape ARchive
tar -cvf archive.tar /path/         # Create
tar -tvf archive.tar                # List contents
tar -xvf archive.tar                # Extract
tar -xvf archive.tar -C /dest/      # Extract to dir

# With compression
tar -czvf archive.tar.gz /path/     # gzip
tar -cjvf archive.tar.bz2 /path/   # bzip2
tar -cJvf archive.tar.xz /path/    # xz (best compression)

# Extract compressed
tar -xzvf archive.tar.gz
tar -xjvf archive.tar.bz2
tar -xJvf archive.tar.xz

# Flags: c=create, x=extract, v=verbose, f=file, z=gzip, j=bzip2, J=xz, t=list

# Standalone compression tools
gzip file.txt               # Creates file.txt.gz, removes original
gzip -d file.txt.gz         # Decompress (or gunzip)
gzip -k file.txt            # Keep original
bzip2 file.txt              # bzip2 compression
xz file.txt                 # xz compression
zip archive.zip file1 file2
unzip archive.zip
unzip archive.zip -d /dest/

# STAR (for SELinux contexts)
star -xattr -H=exustar -c -f=/backup/etc.star /etc/
star -xattr -H=exustar -x -f=/backup/etc.star
```

---

## 20. Text Processing (grep, sed, awk)

```bash
# grep
grep "pattern" file
grep -i "PATTERN" file          # Case insensitive
grep -r "pattern" /etc/         # Recursive
grep -n "pattern" file          # Line numbers
grep -v "pattern" file          # Invert match (NOT)
grep -c "pattern" file          # Count matches
grep -l "pattern" /etc/*        # Only filenames
grep -E "pattern1|pattern2"    # Extended regex (OR)
grep -A 3 "pattern" file        # 3 lines After match
grep -B 3 "pattern" file        # 3 lines Before match
grep -C 3 "pattern" file        # 3 lines around match
grep "^root" /etc/passwd        # Lines starting with root
grep "bash$" /etc/passwd        # Lines ending with bash
grep -w "root" /etc/passwd      # Whole word match

# sed — Stream EDitor
sed 's/old/new/' file                   # Replace first occurrence per line
sed 's/old/new/g' file                  # Replace all
sed 's/old/new/gi' file                 # Case insensitive
sed -i 's/old/new/g' file              # Edit in-place
sed -i.bak 's/old/new/g' file          # In-place with backup
sed '/pattern/d' file                   # Delete matching lines
sed '5d' file                           # Delete line 5
sed '1,5d' file                         # Delete lines 1-5
sed -n '5,10p' file                     # Print lines 5-10
sed 's/^/    /' file                    # Add indent to all lines
sed '/^$/d' file                        # Remove empty lines
sed -n '/start/,/end/p' file            # Print between patterns

# awk
awk '{print $1}' file                   # Print first field
awk '{print $1, $3}' file               # Fields 1 and 3
awk -F: '{print $1}' /etc/passwd        # Custom delimiter (:)
awk -F: '{print $1, $3}' /etc/passwd   # Username and UID
awk '/pattern/{print}' file             # Print matching lines
awk '{print NR, $0}' file              # Print with line numbers
awk 'NF > 0' file                       # Non-empty lines
awk -F: '$3 >= 1000 {print $1}' /etc/passwd  # Users with UID >= 1000
awk '{sum += $1} END {print sum}' file  # Sum first column
awk 'BEGIN{FS=":"} {print $1}' file    # Set FS at beginning
df -h | awk 'NR>1 {print $5, $6}'      # Skip header, print cols

# cut
cut -d: -f1 /etc/passwd         # Field 1 with : delimiter
cut -d: -f1,3 /etc/passwd       # Fields 1 and 3
cut -c1-10 file                 # Characters 1-10

# sort & uniq
sort file                       # Sort alphabetically
sort -n file                    # Sort numerically
sort -rn file                   # Reverse numeric
sort -k2 file                   # Sort by field 2
sort -t: -k3 -n /etc/passwd     # Sort by UID
sort file | uniq                # Remove duplicates
sort file | uniq -c             # Count duplicates
sort file | uniq -d             # Only duplicates

# tr — translate/delete
tr 'a-z' 'A-Z' < file          # Lowercase to uppercase
tr -d '\n' < file               # Delete newlines
tr -s ' ' < file                # Squeeze spaces

# wc — word count
wc -l file                      # Line count
wc -w file                      # Word count
wc -c file                      # Byte count
```

---

## 21. Links (Hard & Symbolic)

```bash
# Hard links
ln source.txt hardlink.txt
# Same inode, same data, both "equal"
# Cannot cross filesystem boundaries
# Cannot link directories

# Symbolic (soft) links
ln -s /path/to/source /path/to/symlink
ln -s /etc/hostname /tmp/myhostname
ls -la /tmp/myhostname          # Shows -> target
readlink /tmp/myhostname        # Show target

# Differences:
# Hard link → same inode, file data persists even if original deleted
# Soft link → different inode, breaks if target deleted (dangling link)

# Check inode numbers
ls -li file1 hardlink.txt       # Same inode number
stat file.txt                   # Full inode info

# Find dangling symlinks
find /path -xtype l             # Broken symlinks
find /tmp -type l               # All symlinks
```

---

## 22. Find & Locate

```bash
# find — real-time search
find /path -name "file.txt"
find /path -name "*.conf"               # Wildcard
find /path -iname "*.CONF"             # Case insensitive
find /path -type f                      # Files only
find /path -type d                      # Directories only
find /path -type l                      # Symlinks only
find /path -user username               # Owned by user
find /path -group groupname             # Owned by group
find /path -size +100M                  # Larger than 100MB
find /path -size -1k                    # Smaller than 1KB
find /path -mtime -7                    # Modified in last 7 days
find /path -mtime +30                   # Modified more than 30 days ago
find /path -newer reference.txt         # Newer than reference file
find /path -perm 644                    # Exact permissions
find /path -perm -4000                  # SUID files
find /path -perm /u+s                   # SUID (alternative)
find /path -empty                       # Empty files/dirs

# Execute commands on found files
find /path -name "*.tmp" -delete        # Delete found files
find /path -name "*.conf" -exec cat {} \;    # Run cat on each
find /path -name "*.conf" -exec cp {} /backup/ \;
find /path -type f -exec chmod 644 {} \;
find / -nouser -o -nogroup              # Files with no owner/group

# locate — fast indexed search
locate filename
locate "*.conf"
updatedb                                # Update database
locate -i FILENAME                     # Case insensitive
locate -n 10 filename                  # Limit results
locate -e filename                     # Only existing files
```

---

## 23. sudo & Privilege Escalation

```bash
# sudo configuration
visudo                          # ALWAYS use visudo (validates syntax)
cat /etc/sudoers
ls /etc/sudoers.d/              # Drop-in files

# /etc/sudoers format:
# user  ALL=(ALL) ALL           # Full sudo
# user  ALL=(ALL) NOPASSWD: ALL # No password
# user  ALL=(root) /bin/systemctl restart httpd  # Specific command
# %wheel ALL=(ALL) ALL          # Group wheel

# Add user to wheel group (RHEL default sudo group)
usermod -aG wheel username

# sudo commands
sudo command                    # Run as root
sudo -u user command           # Run as specific user
sudo -l                        # List allowed commands
sudo -l -U username            # List for specific user
sudo -s                        # Spawn root shell
sudo -i                        # Root shell with env
su -                           # Switch to root
su - username                  # Switch to user

# sudoers defaults
Defaults    requiretty         # Must have terminal
Defaults    !visiblepw         # Hide password
Defaults    env_reset           # Clean environment
Defaults    env_keep += "SSH_AUTH_SOCK"

# Create sudo file in sudoers.d
cat > /etc/sudoers.d/devops << 'EOF'
%devops ALL=(ALL) NOPASSWD: /bin/systemctl, /bin/journalctl
EOF
chmod 440 /etc/sudoers.d/devops  # Must be 440 or 400
```

---

## 24. Time & NTP (chrony)

```bash
# System time
date                            # Current date/time
date +"%Y-%m-%d %H:%M:%S"     # Custom format
date -s "2024-06-01 10:00:00"  # Set date (not recommended)
hwclock                         # Hardware clock
hwclock --systohc              # Sync system time to hardware
hwclock --hctosys              # Sync hardware to system

# timedatectl
timedatectl                     # Show time/zone/NTP status
timedatectl status
timedatectl set-time "2024-06-01 10:00:00"
timedatectl list-timezones
timedatectl list-timezones | grep Asia
timedatectl set-timezone Asia/Kolkata
timedatectl set-ntp true        # Enable NTP sync
timedatectl set-ntp false       # Disable NTP sync

# chrony — NTP client/server (default in RHEL 8/9)
chronyc tracking                # NTP sync status
chronyc sources                 # NTP sources
chronyc sourcestats             # Source statistics
chronyc ntpdata                 # NTP data

# /etc/chrony.conf
server time.example.com iburst  # Add NTP server
pool pool.ntp.org iburst        # Use NTP pool

systemctl enable --now chronyd
systemctl restart chronyd

# Verify sync
timedatectl | grep synchronized
chronyc tracking | grep "System time"
```

---

## 25. Swap Space

```bash
# Create swap file
dd if=/dev/zero of=/swapfile bs=1M count=2048   # 2GB
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
swapon -s                       # Show active swap
free -h                         # Verify

# Persistent swap in /etc/fstab
/swapfile    none    swap    defaults    0 0

# Create swap partition
fdisk /dev/sdb   # Create partition, type: 82 (Linux swap)
mkswap /dev/sdb2
swapon /dev/sdb2

# LVM swap
lvcreate -L 2G -n lv_swap vg_data
mkswap /dev/vg_data/lv_swap
swapon /dev/vg_data/lv_swap

# Disable swap
swapoff /swapfile
swapoff -a                      # Disable all swap

# Swap tuning
sysctl vm.swappiness            # 0=prefer RAM, 100=aggressive swap
sysctl -w vm.swappiness=10
echo "vm.swappiness=10" >> /etc/sysctl.d/99-swap.conf
```

---

## 26. Stratis & VDO (RHEL 8)

### Stratis (RHEL 8)

```bash
dnf install stratisd stratis-cli
systemctl enable --now stratisd

# Create pool
stratis pool create mypool /dev/sdb
stratis pool list
stratis pool add-data mypool /dev/sdc   # Add disk

# Create filesystem
stratis filesystem create mypool myfs
stratis filesystem list
stratis filesystem snapshot mypool myfs myfs-snap   # Snapshot

# Mount
mount /dev/stratis/mypool/myfs /mnt/data

# /etc/fstab for Stratis
UUID=<uuid>  /mnt/data  xfs  defaults,x-systemd.requires=stratisd.service  0  0
```

### VDO — Virtual Data Optimizer (RHEL 8)

```bash
dnf install vdo kmod-kvdo
systemctl enable --now vdo

# Create VDO volume
vdo create --name=myvdo --device=/dev/sdb --vdoLogicalSize=30G
vdo list
vdo status --name=myvdo

# Format and mount
mkfs.xfs -K /dev/mapper/myvdo         # -K = skip discard
mount /dev/mapper/myvdo /mnt/vdo

# /etc/fstab
/dev/mapper/myvdo  /mnt/vdo  xfs  defaults,x-systemd.requires=vdo.service  0  0

# Stats
vdostats --human-readable
vdostats --all
```

---

## 27. AutoFS (Automounting)

```bash
dnf install autofs
systemctl enable --now autofs

# Main config
cat /etc/auto.master
# /mnt/auto  /etc/auto.misc    --timeout=60

# Map file /etc/auto.misc
# key  options  location
data  -fstype=nfs,rw  server:/export/data
usb   -fstype=vfat    :/dev/sdb1

# For NFS home dirs (/etc/auto.master)
/home  /etc/auto.home
# /etc/auto.home:
*  -rw,sync  nfsserver:/home/&

# Direct maps
/etc/auto.master:
/-  /etc/auto.direct

# /etc/auto.direct:
/data  -rw  server:/export/data

systemctl reload autofs
ls /mnt/auto/data          # Accessing triggers mount
```

---

## 28. NFS & Samba (Basic)

### NFS

```bash
# NFS Server
dnf install nfs-utils
systemctl enable --now nfs-server

# /etc/exports
/shared  192.168.1.0/24(rw,sync,no_root_squash)
/data    *(ro,sync)
/home    client.example.com(rw,sync,root_squash)

exportfs -ra                    # Reload exports
exportfs -v                     # Show exports
showmount -e localhost          # Show exports

firewall-cmd --add-service=nfs --permanent
firewall-cmd --add-service=mountd --permanent
firewall-cmd --add-service=rpc-bind --permanent
firewall-cmd --reload

# SELinux for NFS
setsebool -P nfs_export_all_rw on
chcon -t nfs_t /shared

# NFS Client
showmount -e server_ip
mount -t nfs server:/shared /mnt/nfs
mount -o rw,sync server:/shared /mnt/nfs

# /etc/fstab
server:/shared  /mnt/nfs  nfs  defaults,_netdev  0  0
```

### Samba

```bash
dnf install samba samba-client
systemctl enable --now smb nmb

# /etc/samba/smb.conf
[global]
   workgroup = WORKGROUP
   server string = Samba Server

[shared]
   path = /samba/shared
   valid users = user1, @smbgroup
   read only = no
   browsable = yes

# Samba password (separate from system)
smbpasswd -a username

# Firewall
firewall-cmd --add-service=samba --permanent
firewall-cmd --reload

# SELinux for Samba
setsebool -P samba_enable_home_dirs on
chcon -R -t samba_share_t /samba/shared
semanage fcontext -a -t samba_share_t "/samba(/.*)?"
restorecon -Rv /samba/

# Test config
testparm
# Client access
smbclient //server/shared -U username
mount -t cifs //server/shared /mnt/samba -o username=user,password=pass
```

---

## 29. Shell Scripting Basics

```bash
#!/bin/bash
# Shebang line — always first

# Variables
NAME="Linux"
AGE=25
readonly PI=3.14            # Constant
export MYVAR="value"        # Environment variable
echo "Hello $NAME"
echo "Hello ${NAME}World"   # Curly braces for clarity

# Special variables
$0  = Script name
$1-$9 = Positional args
$# = Number of args
$@ = All args (as list)
$* = All args (as string)
$? = Exit status of last command (0=success)
$$ = Current PID
$! = PID of last background process

# Input
read -p "Enter name: " NAME
read -s -p "Password: " PASS    # Silent (no echo)

# Conditionals
if [ condition ]; then
    commands
elif [ condition ]; then
    commands
else
    commands
fi

# Test conditions
[ -f file ]     # File exists and is regular file
[ -d dir ]      # Directory exists
[ -e path ]     # Path exists (any type)
[ -r file ]     # Readable
[ -w file ]     # Writable
[ -x file ]     # Executable
[ -z "$var" ]   # String is empty
[ -n "$var" ]   # String is not empty
[ "$a" = "$b" ] # String equality
[ "$a" != "$b" ]# String not equal
[ $n -eq 5 ]    # Numeric equal
[ $n -ne 5 ]    # Not equal
[ $n -lt 5 ]    # Less than
[ $n -gt 5 ]    # Greater than
[ $n -le 5 ]    # Less than or equal
[ $n -ge 5 ]    # Greater than or equal
[[ condition ]] # Enhanced test (supports &&, ||, =~)

# Loops
for i in 1 2 3 4 5; do
    echo $i
done

for i in $(seq 1 10); do
    echo $i
done

for file in /etc/*.conf; do
    echo $file
done

while [ condition ]; do
    commands
done

until [ condition ]; do
    commands
done

# Functions
my_function() {
    local LOCAL_VAR="hello"    # Local variable
    echo "$1"                   # $1 is function's first arg
    return 0                    # Exit status
}
my_function "argument"

# Arrays
arr=("one" "two" "three")
echo ${arr[0]}
echo ${arr[@]}          # All elements
echo ${#arr[@]}         # Length
arr+=("four")           # Append

# String operations
STR="Hello World"
echo ${#STR}            # Length
echo ${STR:0:5}         # Substring (0,5)
echo ${STR/World/Linux} # Replace
echo ${STR,,}           # Lowercase
echo ${STR^^}           # Uppercase
echo ${STR#Hello }      # Remove prefix
echo ${STR%World}       # Remove suffix

# Exit codes
exit 0      # Success
exit 1      # General error
exit 127    # Command not found

# Script template
#!/bin/bash
set -e              # Exit on error
set -u              # Error on unset variables
set -o pipefail     # Pipe failures propagate

# Arithmetic
result=$((5 + 3))
((counter++))
let "total = a + b"
result=$(echo "scale=2; 10/3" | bc)    # Floating point
```

---

## 30. Exam Tips & Tricks 🎯

### Before the Exam

```
✅ Practice on RHEL 8 or 9 (not CentOS/Fedora for final prep)
✅ Use man pages: man 5 fstab, man semanage-fcontext
✅ Use --help: systemctl --help, firewall-cmd --help
✅ Tab completion is your friend
✅ Read the question TWICE — context matters
```

### Speed Commands

```bash
# Quickly check what's wrong
systemctl status service
journalctl -xe                      # Extended recent logs with explanation
journalctl -u service --since "5 min ago"

# Quick user create with all options
useradd -m -s /bin/bash -G wheel,docker -u 1500 user1
echo "password" | passwd --stdin user1   # Quick password set

# Quick permissions fix
chmod -R 2775 /shared && chown -R :team /shared

# Quick SELinux context fix
restorecon -Rv /path/

# Quick firewall + selinux for new service port
semanage port -a -t http_port_t -p tcp 8888
firewall-cmd --add-port=8888/tcp --permanent && firewall-cmd --reload

# Quick fstab test
mount -a && echo "fstab OK"

# Find which config file is wrong
systemd-analyze verify service-name.service
journalctl -b -p err               # Boot errors only
```

### Common Exam Gotchas

```
⚠️  Always use --permanent with firewall-cmd, then --reload
⚠️  semanage fcontext + restorecon (not just chcon — resets on relabel!)
⚠️  setsebool -P (without -P = temporary, lost on reboot)
⚠️  systemctl daemon-reload after editing unit files
⚠️  partprobe after fdisk changes
⚠️  touch /.autorelabel after changing SELinux user contexts in chroot
⚠️  LVM: XFS cannot be shrunk; ext4 can
⚠️  nmcli con mod needs nmcli con up to apply
⚠️  crontab uses different PATH — use full paths in cron jobs
⚠️  autofs directory must NOT already be mounted
⚠️  podman volumes with -v need :Z for SELinux
```

### Key Config Files

```
/etc/passwd              — User accounts
/etc/shadow              — Password hashes
/etc/group               — Group definitions
/etc/sudoers             — Sudo configuration
/etc/fstab               — Filesystem mounts
/etc/hosts               — Static hostname resolution
/etc/resolv.conf         — DNS config (managed by NM)
/etc/hostname            — System hostname
/etc/selinux/config      — SELinux mode
/etc/ssh/sshd_config     — SSH server config
/etc/chrony.conf         — NTP config
/etc/crontab             — System crontab
/etc/logrotate.conf      — Log rotation
/etc/exports             — NFS exports
/etc/samba/smb.conf      — Samba config
/etc/auto.master         — AutoFS master map
/etc/default/grub        — GRUB2 config
/etc/tuned/tuned.conf    — tuned profile
/boot/grub2/grub.cfg     — Generated GRUB config (BIOS)
```

### Quick Reference: Exam Scenario → Solution

| Scenario | Solution |
|----------|----------|
| Service starts but web page fails | Check SELinux context, boolean |
| Port blocked | firewall-cmd --add-port + reload |
| Non-standard port for service | semanage port -a -t ..._port_t -p tcp PORT |
| File permission denied | Check rwx, SELinux context |
| Cron job not running | Full path? User crontab? cron.allow? |
| LVM space full | lvextend + xfs_growfs or resize2fs |
| Root password forgotten | rd.break at GRUB → chroot → passwd + .autorelabel |
| Container won't write to volume | Add :Z to -v flag |
| SSH key auth fails | chmod 700 ~/.ssh, chmod 600 authorized_keys |
| NTP not syncing | chronyc tracking, check /etc/chrony.conf |
| Package not found | dnf repolist, check repo enabled |

---

## 🏆 Bonus: One-Liners Hall of Fame

```bash
# Find and kill processes by port
fuser -k 8080/tcp

# Watch logs live with highlighting
journalctl -fu nginx | grep --color=always -E "error|warn|"

# Backup with timestamp
tar -czf /backup/etc-$(date +%Y%m%d).tar.gz /etc/

# Monitor disk I/O
iostat -x 1

# Check all failed services
systemctl --failed

# Show all listening ports with process
ss -tulnp

# Find large files
find / -type f -size +500M -exec ls -lh {} \; 2>/dev/null

# Quick disk usage summary
du -sh /* 2>/dev/null | sort -rh | head -10

# Watch a command every 2 seconds
watch -n 2 df -h

# Check open files by service
lsof -c httpd

# Recursively change SELinux type
find /webdata -type f -exec chcon -t httpd_sys_content_t {} \;
# Better:
semanage fcontext -a -t httpd_sys_content_t "/webdata(/.*)?" && restorecon -Rv /webdata

# Quick user audit
awk -F: '$3 >= 1000 {print $1, $3, $7}' /etc/passwd

# Check PAM config
cat /etc/pam.d/sshd
cat /etc/security/pwquality.conf

# Force sync filesystem
sync && echo 3 > /proc/sys/vm/drop_caches
```

---

*Yeh file RHCSA EX200 exam ke liye complete reference hai. Practice karo, man pages dekho, aur SELinux se daro mat! 🐧*

*Last Updated: 2025 | RHEL 8/9 Compatible*
