# Linux Interview Handbook

> **Target Audience:** DevOps Engineers (2–8 Years Experience)
> **Interview Weightage:** ⭐⭐⭐⭐

---

## Table of Contents

- [1. Linux Fundamentals](#1-linux-fundamentals)
- [2. File System Hierarchy](#2-file-system-hierarchy)
- [3. File Permissions](#3-file-permissions)
- [4. User & Group Management](#4-user--group-management)
- [5. Process Management](#5-process-management)
- [6. Service Management (systemd)](#6-service-management-systemd)
- [7. Memory Troubleshooting](#7-memory-troubleshooting)
- [8. CPU Troubleshooting](#8-cpu-troubleshooting)
- [9. Disk Management](#9-disk-management)
- [10. Networking Commands](#10-networking-commands)
- [11. Log Analysis & Text Processing](#11-log-analysis--text-processing)
- [12. SSH & Authentication](#12-ssh--authentication)
- [13. Soft Link vs Hard Link](#13-soft-link-vs-hard-link)
- [14. Linux Troubleshooting Framework](#14-linux-troubleshooting-framework)
- [15. Cron Jobs & Automation](#15-cron-jobs--automation)
- [16. Linux Boot Process](#16-linux-boot-process)
- [17. Interview Questions & Answers](#17-interview-questions--answers)

---

## 1. Linux Fundamentals

Linux is an open-source Unix-like operating system kernel. Most cloud infrastructure, Kubernetes worker nodes, containers, and DevOps tooling run on Linux.

### Key Concepts

| Concept | Description |
| ------- | ----------- |
| Kernel | Core OS component managing hardware, processes, memory |
| Shell | Command interpreter (bash, zsh, sh) |
| File System | Hierarchical structure starting from `/` |
| Everything is a file | Devices, sockets, processes are represented as files |
| Multi-user | Multiple users can work simultaneously |

---

## 2. File System Hierarchy

### Important Directories

| Directory | Purpose |
| --------- | ------- |
| `/` | Root directory - top of the hierarchy |
| `/home` | User home directories |
| `/etc` | System configuration files |
| `/var` | Variable data - logs, mail, databases |
| `/opt` | Third-party applications |
| `/tmp` | Temporary files (cleared on reboot) |
| `/usr` | User binaries, libraries, documentation |
| `/boot` | Boot loader files (kernel, initramfs) |
| `/proc` | Virtual filesystem for process information |
| `/dev` | Device files |
| `/run` | Runtime variable data |
| `/mnt` | Temporary mount points |

---

## 3. File Permissions

### Understanding Permissions

```bash
-rwxr-xr--
```

| Part | Meaning |
| ---- | ------- |
| `-` | File type (`-`=file, `d`=directory, `l`=link) |
| `rwx` | Owner permissions (read, write, execute) |
| `r-x` | Group permissions |
| `r--` | Others permissions |

### Permission Numeric Values

| Permission | Symbol | Value |
| ---------- | ------ | ----- |
| Read | r | 4 |
| Write | w | 2 |
| Execute | x | 1 |
| No permission | - | 0 |

### Common Commands

| Command | Description |
| ------- | ----------- |
| `chmod 755 script.sh` | Owner: rwx, Group: r-x, Others: r-x |
| `chmod 644 file.txt` | Owner: rw-, Group: r--, Others: r-- |
| `chmod +x script.sh` | Add execute permission for all |
| `chmod -R 755 /dir` | Recursively change permissions |
| `chown user:group file` | Change owner and group |
| `chown -R user:group /dir` | Recursively change ownership |

### umask

Defines default permissions for newly created files/directories.

```bash
umask 022  # Files: 644, Directories: 755
```

---

## 4. User & Group Management

| Command | Description |
| ------- | ----------- |
| `useradd devops` | Create a new user |
| `userdel devops` | Delete a user |
| `usermod -aG docker devops` | Add user to a group |
| `passwd devops` | Set/change password |
| `id devops` | Show user ID and group membership |
| `whoami` | Show current user |
| `groups devops` | Show groups for a user |
| `groupadd devops-team` | Create a new group |

---

## 5. Process Management

### Commands

| Command | Description |
| ------- | ----------- |
| `ps -ef` | List all running processes |
| `ps aux` | List processes with resource usage |
| `ps -ef \| grep nginx` | Find specific process |
| `top` | Real-time process monitoring |
| `htop` | Enhanced real-time monitoring |
| `kill PID` | Send SIGTERM (graceful stop) |
| `kill -9 PID` | Send SIGKILL (force kill) |
| `killall nginx` | Kill all processes by name |
| `pgrep -f "java"` | Find PIDs by pattern |
| `pkill -f "app.jar"` | Kill processes by pattern |

### Process States

| State | Meaning |
| ----- | ------- |
| R | Running / Runnable |
| S | Sleeping (waiting for event) |
| D | Uninterruptible sleep (I/O) |
| Z | Zombie (terminated, not reaped) |
| T | Stopped |

---

## 6. Service Management (systemd)

| Command | Description |
| ------- | ----------- |
| `systemctl status nginx` | Check service status |
| `systemctl start nginx` | Start service |
| `systemctl stop nginx` | Stop service |
| `systemctl restart nginx` | Restart service |
| `systemctl reload nginx` | Reload config without restart |
| `systemctl enable nginx` | Enable on boot |
| `systemctl disable nginx` | Disable on boot |
| `systemctl is-active nginx` | Check if running |
| `systemctl is-enabled nginx` | Check if enabled |
| `systemctl list-units --type=service` | List all services |
| `journalctl -u nginx` | View service logs |
| `journalctl -u nginx -f` | Follow service logs |

---

## 7. Memory Troubleshooting

| Command | Description |
| ------- | ----------- |
| `free -h` | Show memory usage (human-readable) |
| `free -m` | Show memory in MB |
| `top` (then press `M`) | Sort processes by memory |
| `ps aux --sort=-%mem \| head` | Top memory consumers |
| `vmstat 1` | Virtual memory statistics |
| `cat /proc/meminfo` | Detailed memory info |

### Interview Answer - Memory Full

Check:
```bash
free -h
ps aux --sort=-%mem | head -10
```

If high memory usage:
1. Identify the process using most memory
2. Check application logs for leaks
3. Restart the service if needed
4. Consider adding more memory or optimizing the app

---

## 8. CPU Troubleshooting

| Command | Description |
| ------- | ----------- |
| `top` (then press `P`) | Sort processes by CPU |
| `ps aux --sort=-%cpu \| head` | Top CPU consumers |
| `uptime` | Load average |
| `lscpu` | CPU info (cores, architecture) |
| `mpstat -P ALL 1` | Per-core CPU usage |

### Load Average

```text
load average: 1.20, 1.50, 2.00
```

| Value | Meaning |
| ----- | ------- |
| 1-min average | Current load |
| 5-min average | Recent trend |
| 15-min average | Historical trend |

**Interpretation:**
- Load < CPU cores → Healthy
- Load = CPU cores → Fully utilized
- Load > CPU cores → Contention (processes waiting)

### Interview Answer - High CPU

1. Run `top` to identify CPU-consuming process
2. Check `ps aux --sort=-%cpu`
3. Review application logs
4. Check if traffic spike (using netstat/ss)
5. Restart service or scale horizontally

---

## 9. Disk Management

| Command | Description |
| ------- | ----------- |
| `df -h` | Filesystem disk usage |
| `df -i` | Inode usage |
| `du -sh *` | Directory sizes in current path |
| `du -sh /var/log` | Size of specific directory |
| `find / -type f -size +500M` | Find files > 500MB |
| `lsblk` | List block devics |
| `fdisk -l` | List disk partitions |

### Interview Scenario - Disk 95% Full

1. Check `. -h`
2. Find large directories: `du -sh /var/*`
3. Check logs: `cd /var/log && ls -lhS`
4. Rotate/compress logs: `logrotate`
5. Remove old archives: `find /var/log -mtime +30 -delete`
6. Extend volume if required

---

## 10. Networking Commands

| Command | Description |
| ------- | ----------- |
| `ip addr` | Check IP addresses |
| `ip route` | Check routing table |
| `ping host` | Test connectivity |
| `curl -v http://host` | Test HTTP endpoint |
| `nslookup host` | DNS query |
| `dig host` | Detailed DNS query |
| `ss -tulpn` | List open ports (modern) |
| `netstat -tulpn` | List open ports (legacy) |
| `nc -zv host port` | Test port connectivity |
| `telnet host port` | Test port connectivity |
| `traceroute host` | Trace network path |
| `tcpdump -i eth0` | Capture network packets |
| `ss -tuan` | List TCP/UDP connections |

### Port Connectivity Check

```bash
# Using nc
nc -zv google.com 80

# Using curl
curl -v telnet://google.com:80

# Using /dev/tcp
echo > /dev/tcp/google.com/80 && echo "Open"
```

---

## 11. Log Analysis & Text Processing

### grep

| Command | Description |
| ------- | ----------- |
| `grep ERROR app.log` | Search for pattern |
| `grep -i error app.log` | Case-insensitive search |
| `grep -r "ERROR" /var/log` | Recursive search |
| `grep -c "ERROR" app.log` | Count matches |
| `grep -v "INFO" app.log` | Exclude matching lines |
| `grep -A5 "ERROR" app.log` | Show 5 lines after match |
| `grep -B5 "ERROR" app.log` | Show 5 lines before match |
| `grep -rl "ERROR" /var/log` | List files containing match |

### awk

| Command | Description |
| ------- | ----------- |
| `awk '{print $1}'` | Print first column |
| `awk '{print $1, $3}'` | Print multiple columns |
| `awk '/ERROR/ {print}'` | Print lines matching ERROR |
| `awk -F: '{print $1}'` | Use custom delimiter |
| `awk '{sum+=$5} END {print sum}'` | Sum column values |

### sed

| Command | Description |
| ------- | ----------- |
| `sed 's/old/new/g' file` | Replace all occurrences|
| `sed -i 's/old/new/g' file` | In-place replace |
| `sed '/pattern/d' file` | Delete matching lines |
| `sed -n '10,20p' file` | Print lines 10-20 |

---

## 12. SSH & Authentication

### Passwordless SSH

```bash
# Generate key pair
ssh-keygen -t rsa -b 4096

# Copy public key to remote server
ssh-copy-id user@server

# Or manually
cat ~/.ssh/id_rsa.pub | ssh user@server "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

### Common SCP Commands

| Command | Description |
| ------- | ----------- |
| `scp file.txt user@host:/tmp` | Copy local → remote |
| `scp user@host:/tmp/file.txt .` | Copy remote → local |
| `scp -r /dir user@host:/tmp` | Copy directory recursively |
| `rsync -avz /src user@host:/dest` | Sync files (resumable)|

---

## 13. Soft Link vs Hard Link

| Feature | Soft Link (symlink) | Hard Link |
| ------- | ------------------- | --------- |
| Command | `ln -s file1 file2` | `ln file1 file2` |
| Inode | Different inode | Same inode |
| Points to | Path of original file | Data on disk|
| Original deleted | Link breaks (dangling) | Still works |
| Across filesystems | Yes | No |
| For directories | Yes | No |

---

## 14. Linux Troubleshooting Framework

### Server Slow - Systematic Check

```bash
# 1. CPU
top

# 2. Memory
free -h

# 3. Disk
df -h

# 4. I/O
iostat -xz 1

# 5. Network
sar -n DEV 1

# 6. Check for zombie processes
ps aux | grep Z

# 7. Check system logs
journalctl -xe
```

### Troubleshooting Checklist

| Issue | Check |
| ----- | ----- |
| High CPU | `top`, `ps aux --sort=-%cpu` |
| High Memory | `free -h`, `ps aux --sort=-%mem` |
| Disk Full | `df -h`, `du -sh /*` |
| High I/O Wait | `iotop`, `iostat -xz 1` |
| Network Slow | `ss -tan`, `traceroute`, `mtr` |
| Service Down | `systemctl status`, `journalctl -u` |
| Application Crash | `tail -f /var/log/app.log` |

---

## 15. Cron Jobs & Automation

### Crontab Format

```text
* * * * * command
| | | | |
| | | | +-- Day of week (0-7, Sun=0/7)
| | | +---- Month (1-12)
| | +------ Day of month (1-31)
| +-------- Hour (0-23)
+---------- Minute (0-59)
```

### Examples

```bash
# Every minute
* * * * * /script/check.sh

# Daily at 2 AM
0 2 * * * /script/backup.sh

# Every Sunday at 3 AM
0 3 * * 0 /script/maintenance.sh

# Every 5 minutes
*/5 * * * * /script/health-check.sh

# First day of month at midnight
0 0 1 * * /script/monthly-report.sh
```

### Cron Management

| Command | Description |
| ------- | ----------- |
| `crontab -e` | Edit crontab |
| `crontab -l` | List crontab entries|
| `crontab -r` | Remove crontab |
| `cat /var/spool/cron/user` | View user's crontab directly|

---

## 16. Linux Boot Process

```text
BIOS/UEFI
   ↓
Boot Loader (GRUB2)
   ↓
Kernel (vmlinz)
   ↓
nitramfs (initial ram filesystem)
   ↓
nit Process (systemd)
   ↓
Systemd Units / Services
   ↓
Login Prompt
```

**Key Points:**
1. **BIOS/UEFI** — Hardware initialization
2. **GRUB2** — Boot loader, chooses kernel
3. **Kernel** — Loads into memory, initializes devices
4. **Initramfs** — Temporary root fs for hardware loading
5. **systemd** — First process (PID 1), starts all services

---

## 17. Interview Questions & Answers

### Q1. What is Load Average?

**Answer:**
Load average represents the average number of processes waiting for CPU over 1, 5, and 15 minutes. For a 4-core CPU, load of 4 means fully utilized. Above 4 indicates contention. Below 4 is healthy.

### Q2. How do you troubleshoot high CPU?

**Answer:**
1. Run `top` and press `P` to sort by CPUusage
2. Identify the PID consuming most CPU
3. Check `ps -fp <PID>` for process details
4. Review application logs
5. Check `strace -p <PID>` for system calls
6. Restart or kill the process if necessary

### Q3. How do you troubleshoot high memory?

**Answer:**
1. Run `free -h` to check overall memory
2. Run `ps aux --sort=-%mem` to identify top consumers
3. Check for memory leaks in application logs
4. Check `cat /proc/meminfo` for detailed breakdown
5. Consider swapping activity (`vmstat`)

### Q4. How do you troubleshoot full disk?

**Answer:**
1. Run `df -h` to identify full partition
2. Run `du -sh /*` to find large directories
3. Check `/var/log` for large log files
4. Rotate/compress logs with `logrotate`
5. Remove old temporary files
6. Find large files with `find / -type f -size +500M`

### Q5. Diffence between soft link and hard link?

**Answer:**
A soft link (symlink) points to the file path and breaks if the original is deleted. A hard link points to the inode and continues working even if the original filename is deleted. Soft links can cross lesystems; hard links cannot.

### Q6. Difference between grep, awk, and sed?

| Tool | Purpose |
| ---- | ------- |
| `grep` | Search and filter text by pattern |
| `awk` | Column-based processing and reporting |
| `sed` | Text transformation and substitution |

**Answer:**
- `grep` is for **searching** patterns
- `awk` is for **processing** structured data (columns)
- `sed` is for **editing** text (replace, delete)

### Q7. How do you check open ports?

**Answer:**
```bash
ss -tulpn
# or
netstat -tulpn
```

`-t` = TCP, `-u` = UDP, `-l` = listening, `-p` = process, `-n` = numeric

### Q8. How does passwordless SSH work?

**Answer:**
1. Generate a key pair: `ssh-keygen` (public + private keys)
2. Copy public key to server: `ssh-copy-id user@server`
3. The public key is added to `~/.ssh/authorized_keys` on the server
4. When connecting, the client proves possession of the private key
5. The server verifies using the stored public key

### Q9. What is the difference between `kill` and `kill -9`?

**Answer:**
- `kill <PID>` sends SIGTERM (signal 15) — graceful shutdown, allows cleanup
- `kill -9 <PID>` sends SIGKILL (signal 9) — force kill, immediate termination

Always try `kill` first; use `kill -9` only if the process is unresponsive.

### Q10. What is stored under /var?

**Answer:**
The `/var` directory contains variable data:
- `/var/log` — System and application logs
- `/var/mail` — User emails
- `/var/spool` — Print/cron spools
- `/var/lib` — Database and application data
- `/var/tmp` — Temporary files (persisted between reboots)
- `/var/cache` — Application cache data

### Q11. What is `strace` and how do you use it?

**Answer:**
`strace` traces system calls made by a process. Used for debugging:
```bash
strace -p <PID>       # Attach to running process
strace -c -p <PID>     # Count system calls
strace -e trace=file <PID>  # Trace only file operations
```

### Q12. How do you debug a Bash script?

**Answer:**
```bash
# Debug mode
bash -x script.sh

# Add to script
set -x
# ... commands ...
set +x

# Check syntax without executing
bash -n script.sh
```

### Q13. What is the Linux boot process?

**Answer:**
1. **BIOS/UEFI** — Hardware initialization, POST
2. **GRUB2** — Boot loader loads the kernel
3. **Kernel** — Mounts root filesystem, initializes devices
4. **initramfs** — Temporary fs for driver loading
5. **systemd** — PID 1, takes over, starts services
6. **Login prompt** — System ready

### Q14. How do you find and delete files older than 30 days?

**Answer:**
```bash
# Find and delete
find /var/log -type f -mtime +30 -delete

# Preview first
find /var/log -type f -mtime +30

# With confirmation
find /var/log -type f -mtime +30 -exec -i {} \;
```

### Q15. What is the difference between `/tmp` and `/var/tmp`?

**Answer:**
- `/tmp` — Cleared on system reboot
- `/var/tmp` — Persisted across reboots

---

## Quick Revision

| Topic | Command/Concept |
| ----- | --------------- |
| File permissions | `chmod 755`, `chown user:group` |
| Process | `ps -ef`, `top`, `kill PID` |
| Services | `systemctl start/stop/status/restart` |
| Disk | `df -h`, `du -sh` |
| Network | `ss -tulpn`, `ip addr`, `ping` |
| Memory | `free -h`, `ps aux --srt=-%mem` |
| CPU | `top`, `uptime`, `ps aux --sort=-%cpu` |
| Logs | `tail -f`, `grep ERROR`, `journalctl` |
| Text | `grep`, `awk`, `sed` |
| SSH | `ssh-keygen`, `ssh-copy-id` |
| Links | `ln -s` (soft), `ln` (hard) |
| Cron | `crontab -e`, `crontab -l` |