# Linux System Monitoring Basics

A beginner-friendly guide to the essential Linux commands for checking processes, CPU, memory, and disk usage: `ps`, `top`, `free`, `df`, and `du`.

## Contents

1. [ps](#1-ps--snapshot-of-running-processes)
2. [top](#2-top--live-process-view)
3. [CPU usage](#3-cpu-usage)
4. [Memory usage](#4-memory-usage-free--h)
5. [df](#5-df--disk-space-per-filesystem)
6. [du](#6-du--disk-space-per-folder)
7. [Troubleshooting workflow](#7-troubleshooting-workflow)
8. [Quick cheat sheet](#8-quick-cheat-sheet)

---

## 1. ps : snapshot of running processes

```
$ ps aux | head -3
USER   PID  %CPU %MEM COMMAND
root     1   0.0  0.1 /sbin/init
alex  2140  25.3  4.2 firefox
```

- `PID` is the process ID; `%CPU` and `%MEM` show resource usage.
- Find one program: `ps aux | grep firefox`
- Top 5 memory users: `ps aux --sort=-%mem | head -6`
- Show parent PIDs: `ps -ef`

## 2. top : live process view

Refreshes every few seconds, like Task Manager.

```
$ top
load average: 0.50, 0.70, 0.90
%Cpu(s): 10.0 us, 3.0 sy, 85.0 id, 2.0 wa
MiB Mem: 8000 total, 500 free, 3000 used, 4500 buff/cache

PID   USER  %CPU %MEM COMMAND
2140  alex  25.3  4.2 firefox
```

| Key | Action |
|-----|--------|
| `P` | Sort by CPU |
| `M` | Sort by memory |
| `k` | Kill a process |
| `q` | Quit |

Tip: `htop` is a friendlier alternative if installed.

## 3. CPU usage

Read the `%Cpu(s)` line in `top`:

| Field | Meaning | Example |
|-------|---------|---------|
| `us` | User programs | 10% |
| `sy` | Kernel | 3% |
| `id` | Idle | 85% (CPU is ~15% busy) |
| `wa` | Waiting on disk I/O | 2% (high = disk bottleneck) |

**Load average** (1, 5, 15 minutes): compare it to your core count (`nproc`). On a 4-core machine: 0.5 is relaxed, 4.0 is fully busy, 8.0 is overloaded.

## 4. Memory usage: `free -h`

```
$ free -h
        total   used   free   available
Mem:     7.8G   3.0G   0.5G      4.6G
Swap:    2.0G     0B   2.0G
```

Look at **available**, not free. Linux uses idle RAM as disk cache and releases it when programs need it. Worry only if *available* is very low or swap is heavily used.

## 5. df : disk space per filesystem

```
$ df -h
Filesystem  Size  Used  Avail  Use%  Mounted on
/dev/sda1    50G   47G   3G    95%   /
```

- `-h` gives human-readable sizes.
- Watch `Use%`: near 100% causes problems.
- `df -i` checks inode usage (can fill up even with free space).

## 6. du : disk space per folder

```
$ du -h --max-depth=1 /var | sort -h
200M  /var/cache
8.5G  /var/log
9.0G  /var
```

- `du -sh /var/log` shows the total size of one folder.
- Here `/var/log` is the culprit at 8.5G.

## 7. Troubleshooting workflow

Server is slow? Check in this order:

1. `top`: which process is using CPU or memory?
2. `free -h`: is RAM running out?
3. `df -h`: is a disk full?
4. `du -h --max-depth=1 /`: which folder filled it? Repeat inside that folder.

## 8. Quick cheat sheet

| Goal | Command |
|------|---------|
| List all processes | `ps aux` |
| Find a process | `ps aux \| grep name` |
| Live monitoring | `top` |
| Memory overview | `free -h` |
| Number of CPU cores | `nproc` |
| Disk space per filesystem | `df -h` |
| Folder size | `du -sh folder/` |
| Largest subfolders | `du -h --max-depth=1 . \| sort -h` |
