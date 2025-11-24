# bpftrace-sendto

The bpftrace script that helps to trace full payload that included destination address of sendto() syscall.

In this script, I used mysqld process as an example that I will create a tracing on it. Everyone can use the same mechanism to trace another process.

## Requirements

- bpftrace is installed

## Flow

```text
socket(...):sockfd -> accept(int sockfd, ...):sockfd_accepted -> sendto(int sockfd_accepted, ...)
```

## Usage

Trace running processes have comm=mysqld

```bash
~# bpftrace trace-mysqld.bt -- mysqld # trace process with comm = mysqld

...
pid=29664 tid=29963 comm=connection fd=35 msg=J\x00\x00\x00\x0a8.0.31\x00s\x82\x00\x008\x15p@m)2#\x00\xff\xf7\xff\x02\x00\xff\x9f\x15\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00^d\x11\x0eLh!D\x1cd\x01]\x00caching_sha2_password\x00at\x00\x00\x00\x00\x00\x00\x00\x00\xb8\x97\x00\q\x7f\x00\x00\xb8\x97\x00\q\x7f\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00O\x00\x00\x00ibmy\x00\x00\x00\x00_cli len=78 flg=64 addr_len=0 family=1 dest=localhost
pid=29664 tid=29963 comm=connection fd=35 msg=G\x00\x00\x02\xff\x15\x04#28000Access denied for user 'root'@'localhost' (using password: NO)_platform\x06x86_64\x03_os\x05Linux\x0c_client_name\x08libmysql\x0f_cli len=75 flg=64 addr_len=0 family=1 dest=localhost
pid=29664 tid=29963 comm=connection fd=35 msg=3\x00\x00\x03\xff\x86\x04#08S01Got an error reading communication packets#\xa1\xaf]S\xe8\xd3\x81+J\xb1\xd0\xc2\xf1\xf5caching_sha2_password\x00t\x04_pid\x073062534\x09_platform\x06x86_64\x03_os\x05 len=55 flg=64 addr_len=0 family=10 dest=::ffff:10.196.6.35
```

Trace all running processes

```bash
~# bpftrace trace-mysqld.bt -- . # trace all running process

...
pid=365 tid=365 comm=systemd-resolve fd=15 msg={"parameters":{"addresses":[{"ifindex":2,"family":2,"address":[140,82,114,22]}],"name":"glb-db52c2cf8be544.github.com","flags":1 sa_family=0
pid=29664 tid=29963 comm=connection fd=35 msg=G\x00\x00\x02\xff\x15\x04#28000Access denied for user 'root'@'localhost' (using password: NO)
pid=365 tid=365 comm=systemd-resolve fd=15 msg={"parameters":{"addresses":[{"ifindex":2,"family":2,"address":[20,205,243,166]}],"name":"github.com","flags":8388609}}\x00\x00\x81\x00\x00\x00\x00\x00\x00\x00 sa_family=0
pid=365 tid=365 comm=systemd-resolve fd=26 msg={"parameters":{"addresses":[{"ifindex":2,"family":2,"address":[20,205,243,166]}],"name":"github.com","flags":8388609}}\x00\x00\x91(\x00\x00\x00\x00\x00\x00 sa_family=0
```