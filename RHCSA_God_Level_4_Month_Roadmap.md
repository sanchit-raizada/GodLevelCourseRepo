# RHCSA God-Level 4-Month Roadmap

## Goal
Sirf RHCSA pass nahi — Linux ko andar se samajhna.

Kernel → boot → process → storage → networking → systemd → automation → troubleshooting → real admin mindset.

---

# MONTH 1 — LINUX INTERNALS + SHELL + FILESYSTEMS

> “Linux actually kaam kaise karta hai?”

---

# WEEK 1 — Computer → Kernel → User Space

## Must Learn

### Boot Flow

```text
BIOS/UEFI
→ Bootloader (GRUB)
→ Kernel
→ initramfs
→ PID 1 (systemd)
→ services
→ login
```

## Deep Topics

- Kernel kya hai
- User space vs kernel space
- System calls
- libc
- shell ka role
- process creation
- fork()
- exec()
- PID
- PPID

## God-Level Concepts

- syscall tracing
- `/proc`
- `/sys`
- kernel modules
- monolithic kernel
- why Linux fast hai

## Commands

```bash
uname
dmesg
lsmod
modprobe
lscpu
cat /proc/cpuinfo
strace
pstree
ps
top
htop
free
vmstat
uptime
```

## MUST UNDERSTAND

```text
bash → syscall → VFS → filesystem → block layer → driver → hardware
```

---

# WEEK 2 — Filesystem Internals

## Must Learn

- inode
- superblock
- data blocks
- journaling
- ext4 basics
- xfs basics
- VFS layer

## Deep Topics

- hard link
- symbolic link
- file descriptors
- stdin stdout stderr
- permissions internals
- ACLs
- umask

## Commands

```bash
stat
ls -li
df
du
mount
umount
findmnt
blkid
file
lsof
chmod
chown
setfacl
getfacl
```

## GOD LEVEL

Understand:

```text
rm file
```

actually kya karta hai internally.

---

# WEEK 3 — Shell Mastery

## Must Learn

- bash parsing
- quoting
- expansion
- variables
- environment
- pipes
- redirects

## Advanced

- subshell
- process substitution
- xargs
- regex
- job control

## Commands

```bash
grep
awk
sed
cut
sort
uniq
tee
tr
xargs
```

## GOD LEVEL

Build:

- log analyzer
- failed login detector
- memory monitor script

---

# WEEK 4 — Process + Memory + Scheduling

## Learn

- processes
- threads
- daemon
- nice
- scheduler
- signals
- zombie process

## Deep

- cgroups basics
- memory layout
- virtual memory
- swap
- OOM killer

## Commands

```bash
kill
pkill
nice
renice
jobs
bg
fg
nohup
systemctl status
```

## GOD LEVEL

Explain:

```text
Why zombie process exists?
```

---

# MONTH 2 — STORAGE + BOOT + SYSTEMD

---

# WEEK 5 — Partitions + Filesystems

## Learn

- MBR vs GPT
- partitions
- filesystem creation
- mounting

## Commands

```bash
fdisk
parted
mkfs.xfs
mkfs.ext4
mount
fstab
xfs_info
```

## Practice

- add new disk
- auto mount
- recover broken fstab

---

# WEEK 6 — LVM (VERY IMPORTANT)

## Must Learn

```text
PV → VG → LV
```

## Deep

- resizing
- snapshots
- online expansion

## Commands

```bash
pvcreate
vgcreate
lvcreate
lvextend
xfs_growfs
resize2fs
```

## GOD LEVEL

Understand:
Why enterprises use LVM.

---

# WEEK 7 — systemd Mastery

## Learn

- targets
- units
- journald
- dependencies

## Commands

```bash
systemctl
journalctl
hostnamectl
timedatectl
loginctl
```

## Deep

- PID 1
- boot targets
- rescue mode
- emergency mode

## Practice

- create custom service
- debug failed service

---

# WEEK 8 — Boot Troubleshooting

## Learn

- GRUB
- initramfs
- kernel panic
- rescue boot

## Practice

- reset root password
- broken fstab recovery
- failed boot repair

## GOD LEVEL

Understand:
Why initramfs needed before root mount.

---

# MONTH 3 — NETWORKING + SECURITY + USERS

---

# WEEK 9 — Networking Internals

## Learn

- IP
- subnetting
- routing
- DNS
- gateway
- ARP

## Commands

```bash
ip
ss
ping
traceroute
dig
nmcli
tcpdump
```

## GOD LEVEL

Understand packet flow:

```text
Application → TCP/IP stack → NIC → switch
```

---

# WEEK 10 — Users + Authentication

## Learn

- passwd
- shadow
- PAM basics
- groups
- sudo

## Commands

```bash
useradd
passwd
id
groups
visudo
chage
```

## Deep

- authentication flow
- password hashing
- sudo internals

---

# WEEK 11 — Permissions + SELinux

## MUST LEARN

SELinux ignore mat karna.

## Learn

- contexts
- labels
- enforcing
- booleans

## Commands

```bash
getenforce
restorecon
semanage
chcon
ausearch
```

## GOD LEVEL

Understand:
Why chmod 777 still fail under SELinux.

---

# WEEK 12 — Firewalld + SSH

## Learn

- zones
- ports
- services
- ssh hardening

## Commands

```bash
firewall-cmd
ssh
scp
sftp
```

## Practice

- key authentication
- disable password login
- custom SSH port

---

# MONTH 4 — AUTOMATION + TROUBLESHOOTING + RHCSA LABS

---

# WEEK 13 — Bash Scripting

## Learn

- conditions
- loops
- functions
- exit codes

## Build

- backup script
- health checker
- disk monitor

---

# WEEK 14 — Automation

## Learn

- cron
- timers
- log rotation

## Commands

```bash
crontab
at
systemd timers
logrotate
```

---

# WEEK 15 — Real RHCSA Labs

## Do Daily

- user management
- permissions
- LVM
- networking
- boot recovery
- SELinux fixing

## Time Yourself

Real exam pressure.

---

# WEEK 16 — Troubleshooting God Mode

## Practice Failures

- broken boot
- full disk
- permission denied
- failed service
- DNS issue
- SELinux block
- network unreachable

## Goal

```text
symptom → subsystem → logs → root cause
```

---

# DAILY RULES

## EVERY DAY

### 1 Hour
Theory

### 2 Hour
Hands-on

### 1 Hour
Break/fix labs

---

# MUST BUILD LAB

Use:

- Oracle VirtualBox
OR
- VMware Workstation

Create:

- 2 Linux VMs
- one server
- one client

---

# AFTER THIS ROADMAP

You will understand:

- kernel flow
- process model
- Linux boot
- storage stack
- networking internals
- systemd
- permissions
- SELinux
- admin troubleshooting

Then:

RHCSA → RHCE → DevOps → SRE → Platform Engineer

Aur tab Linux “commands” nahi lagega.

Ek living operating system lagega.
