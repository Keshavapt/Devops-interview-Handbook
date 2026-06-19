# Linux Interview Handbook

## Linux Fundamentals

### What is Linux?

Linux is an open-source Unix-like operating system kernel. Most cloud infrastructure, Kubernetes worker nodes, containers, and DevOps tooling run on Linux.

---

## Linux File System

### Important Directories

| Directory | Purpose                     |
| --------- | --------------------------- |
| /         | Root directory              |
| /home     | User home directories       |
| /etc      | Configuration files         |
| /var      | Logs and variable data      |
| /opt      | Third-party applications    |
| /tmp      | Temporary files             |
| /usr      | User binaries and libraries |
| /boot     | Boot loader files           |
| /proc     | Process information         |
| /dev      | Device files                |

### Interview Question

**What is stored under /var?**

Answer:

The `/var` directory contains variable data such as:

- Logs (`/var/log`)
- Mail queues
- Database files
- Cache files
- Spool files

Example:

```bash
cd /var/log
ls
```

---

## File Permissions

### Permission Types

```bash
-rwxr-xr--
```

| Symbol | Meaning |
| ------ | ------- |
| r      | Read    |
| w      | Write   |
| x      | Execute |

### Permission Groups

```text
Owner Group Others
```

Example:

```bash
chmod 755 file.sh
```

Meaning:

```text
Owner  = rwx
Group  = r-x
Others = r-x
```

---

### chmod Commands

```bash
chmod 755 script.sh
chmod 644 file.txt
chmod +x script.sh
```

---

## User Management

### Create User

```bash
useradd devops
passwd devops
```

### Delete User

```bash
userdel devops
```

### Check User

```bash
id devops
```

---

## Process Management

### View Running Processes

```bash
ps -ef
```

### Real-Time Monitoring

```bash
top
```

or

```bash
htop
```

---

### Find Specific Process

```bash
ps -ef | grep nginx
```

---

### Kill Process

```bash
kill PID
```

Force Kill

```bash
kill -9 PID
```

---

## Service Management

### Check Service Status

```bash
systemctl status nginx
```

### Start Service

```bash
systemctl start nginx
```

### Stop Service

```bash
systemctl stop nginx
```

### Restart Service

```bash
systemctl restart nginx
```

### Enable on Boot

```bash
systemctl enable nginx
```

---

## Memory Troubleshooting

### Check Memory

```bash
free -h
```

Example Output:

```text
total used free
16G   10G  6G
```

---

### Top Memory Consumers

```bash
top
```

or

```bash
ps aux --sort=-%mem | head
```

---

## CPU Troubleshooting

### Check CPU Usage

```bash
top
```

### Load Average

```bash
uptime
```

Example:

```text
load average: 1.20, 1.50, 2.00
```

Meaning:

- 1 minute average
- 5 minute average
- 15 minute average

---

### Interview Question

**What is Load Average?**

Answer:

Load average represents the average number of processes waiting for CPU resources.

If a server has 4 CPU cores:

```text
Load = 4
```

means fully utilized.

```text
Load > 4
```

means CPU contention.

---

## Disk Management

### Check Disk Usage

```bash
df -h
```

### Check Directory Size

```bash
du -sh *
```

### Largest Files

```bash
find / -type f -size +500M
```

---

### Scenario

Question:

```text
/var partition is 95% full.
What will you do?
```

Answer:

1. Check disk usage

```bash
df -h
```

2. Identify large directories

```bash
du -sh /var/*
```

3. Check logs

```bash
cd /var/log
```

4. Rotate or compress logs

```bash
logrotate
```

5. Remove unnecessary files

6. Extend volume if required

---

## Networking Commands

### Check IP

```bash
ip addr
```

or

```bash
ifconfig
```

---

### Check Routes

```bash
ip route
```

---

### DNS Lookup

```bash
nslookup google.com
```

```bash
dig google.com
```

---

### Connectivity Test

```bash
ping google.com
```

---

### Port Connectivity

```bash
telnet host port
```

or

```bash
nc -zv host port
```

---

### Open Ports

```bash
netstat -tulpn
```

or

```bash
ss -tulpn
```

---

## Log Analysis

### View Logs

```bash
cat app.log
```

### Follow Logs

```bash
tail -f app.log
```

### Last 100 Lines

```bash
tail -100 app.log
```

### Search Errors

```bash
grep ERROR app.log
```

---

## grep, awk and sed

### grep

Search text

```bash
grep ERROR app.log
```

---

### awk

Column processing

```bash
awk '{print $1}'
```

---

### sed

Text replacement

```bash
sed 's/old/new/g'
```

---

## SSH

### Connect Server

```bash
ssh user@host
```

### Copy File

```bash
scp file.txt user@server:/tmp
```

---

## Passwordless Authentication

Generate Key

```bash
ssh-keygen
```

Copy Key

```bash
ssh-copy-id user@server
```

---

## Soft Link vs Hard Link

### Soft Link

```bash
ln -s file1 file2
```

Characteristics:

- Points to original file
- Breaks if original deleted

---

### Hard Link

```bash
ln file1 file2
```

Characteristics:

- Same inode
- Survives original filename deletion

---

## Linux Troubleshooting Framework

### Server Slow

Check:

```bash
top
free -h
df -h
iostat
vmstat
```

Look for:

- High CPU
- Memory pressure
- Disk full
- I/O wait
- Network latency

---

## Frequently Asked Commands

```bash
pwd
ls -ltr
cd
mkdir
rm -rf
cp
mv
find
grep
awk
sed
tail
head
cat
less
top
free
df
du
systemctl
journalctl
ps
kill
chmod
chown
ssh
scp
curl
wget
tar
zip
unzip
```

---

# Interview Cheat Sheet

### Top 15 Questions

1. Difference between soft link and hard link.
2. What is load average?
3. How do you troubleshoot high CPU?
4. How do you troubleshoot high memory?
5. How do you troubleshoot full disk?
6. Difference between grep, awk, sed.
7. How do you find large files?
8. How do you check running processes?
9. How do you restart a service?
10. How does SSH work?
11. What is passwordless authentication?
12. How do you check open ports?
13. What is stored under /var?
14. How do you troubleshoot a slow server?
15. Explain Linux boot process at a high level.
