# Netcat-nc-
If you're learning networking or doing authorized security testing, **Netcat (`nc`)** is one of the most useful tools on Kali Linux. It can act as a TCP/UDP client or listener, test ports, transfer files, and troubleshoot network services.

### 1. Check Netcat

```bash
nc -h
```

On Kali, you may have OpenBSD Netcat:

```bash
nc -h 2>&1 | head
```

### 2. Test whether a port is open

```bash
nc -vz 192.168.1.10 80
```

For a range of ports:

```bash
nc -vz 192.168.1.10 20-100
```

* `-v` = verbose
* `-z` = scan without sending application data

For UDP:

```bash
nc -vzu 192.168.1.10 53
```

### 3. Create a simple TCP client/server

**Terminal 1 — listener:**

```bash
nc -lvnp 4444
```

**Terminal 2 — connect:**

```bash
nc 127.0.0.1 4444
```

Now anything typed in one terminal should appear in the other.

This is a great way to understand TCP connections.

### 4. Transfer a file between two machines

On the receiving machine:

```bash
nc -lvnp 9000 > received.txt
```

On the sending machine:

```bash
nc 192.168.1.20 9000 < file.txt
```

For a binary file:

```bash
nc -lvnp 9000 > received.bin
```

```bash
nc 192.168.1.20 9000 < file.bin
```

Verify the transfer:

```bash
sha256sum file.bin
sha256sum received.bin
```

### 5. Send a simple HTTP request

```bash
printf 'GET / HTTP/1.1\r\nHost: example.com\r\nConnection: close\r\n\r\n' | nc example.com 80
```

This is useful for understanding what actually happens underneath tools such as `curl`.

### 6. Check a service manually

For example, if you have an SMTP server in your own lab:

```bash
nc 192.168.1.20 25
```

You might receive a banner such as:

```text
220 mail.example.local ESMTP
```

You can similarly connect to other text-based services and inspect their banners/protocol behavior.

### 7. Use Netcat for a basic connectivity test

Machine A:

```bash
nc -lvnp 5555
```

Machine B:

```bash
nc -v 192.168.1.10 5555
```

If it doesn't connect, check:

```bash
ip addr
```

and:

```bash
sudo ss -lntup
```

Also check your firewall.

### 8. Useful options to remember

| Option | Meaning            |
| ------ | ------------------ |
| `-l`   | Listen             |
| `-v`   | Verbose            |
| `-n`   | Don't resolve DNS  |
| `-p`   | Specify local port |
| `-z`   | Port scanning mode |
| `-u`   | UDP                |
| `-w`   | Connection timeout |
| `-4`   | IPv4               |
| `-6`   | IPv6               |

### A good Kali practice lab
Absolutely. Here’s a practical **Netcat (nc) cheat sheet for Kali Linux**, focused on networking and authorized lab testing.

## 🐱 Netcat Kali Linux Cheat Sheet

### 1. Basic syntax

```bash
nc [options] host port
```

Check your installed version:

```bash
nc -h
```

---

## 🔌 TCP connections

### 2. Connect to a TCP port

```bash
nc 192.168.1.10 80
```

### 3. Test whether a port is listening

```bash
nc -vz 192.168.1.10 80
```

Example:

```text
Connection to 192.168.1.10 80 port [tcp/http] succeeded!
```

### 4. Test multiple ports

```bash
nc -vz 192.168.1.10 20-100
```

For a larger range:

```bash
nc -vz 192.168.1.10 1-1000
```

For serious port discovery, **Nmap is generally the better tool**.

---

## 👂 Create a listener

### 5. TCP listener

On Machine A:

```bash
nc -lvnp 4444
```

Then from Machine B:

```bash
nc 192.168.1.10 4444
```

You now have a basic TCP communication channel.

### 6. Listen on a specific address

```bash
nc -lvnp 4444 -s 192.168.1.10
```

---

## 📁 File transfer

### 7. Receive a file

Machine A:

```bash
nc -lvnp 9000 > received.txt
```

Machine B:

```bash
nc 192.168.1.10 9000 < file.txt
```

### 8. Transfer a binary file

Receiver:

```bash
nc -lvnp 9000 > backup.bin
```

Sender:

```bash
nc 192.168.1.10 9000 < backup.bin
```

Verify:

```bash
sha256sum backup.bin
sha256sum received.bin
```

The hashes should match.

---

## 🌐 HTTP testing

### 9. Manually send an HTTP request

```bash
printf 'GET / HTTP/1.1\r\nHost: 192.168.1.10\r\nConnection: close\r\n\r\n' | nc 192.168.1.10 80
```

This helps you understand how HTTP works at the TCP level.

### 10. Capture an incoming HTTP request

Start:

```bash
nc -lvnp 8080
```

Then visit from another machine:

```text
http://192.168.1.10:8080/
```

You'll see the HTTP request in your Netcat terminal.

---

## 📡 UDP

### 11. UDP listener

```bash
nc -lvnup 9999
```

### 12. Send UDP data

From another machine:

```bash
echo "Hello UDP" | nc -u 192.168.1.10 9999
```

UDP behaves differently from TCP, so don't expect the same connection-oriented behavior.

---

## ⏱️ Timeouts

### 13. Set a timeout

```bash
nc -vz -w 3 192.168.1.10 443
```

Here `-w 3` gives Netcat a 3-second timeout.

---

## 🔎 Service/banner testing

### 14. Connect to SSH

```bash
nc 192.168.1.10 22
```

You may see something similar to:

```text
SSH-2.0-OpenSSH_...
```

### 15. Connect to SMTP

In your own lab:

```bash
nc 192.168.1.20 25
```

### 16. Connect to a web server

```bash
nc 192.168.1.20 80
```

Then type:

```http
GET / HTTP/1.1
Host: 192.168.1.20

```

---

## 🛠️ Troubleshooting

### 17. See listening sockets

```bash
sudo ss -lntup
```

### 18. Find your IP

```bash
ip addr
```

or:

```bash
hostname -I
```

### 19. Check routing

```bash
ip route
```

### 20. Check whether your firewall is blocking traffic

On systems using UFW:

```bash
sudo ufw status
```

---

# 🔥 A useful home-lab exercise

Create two VMware machines:

```text
┌──────────────────┐       ┌──────────────────┐
│     Kali A       │       │     Kali B       │
│                  │       │                  │
│ nc -lvnp 4444    │◄─────►│ nc <IP-A> 4444   │
│                  │  TCP  │                  │
└──────────────────┘       └──────────────────┘
```

On **Kali A**:

```bash
nc -lvnp 4444
```

On **Kali B**:

```bash
nc <KALI-A-IP> 4444
```

Then experiment with:

1. TCP communication
2. UDP communication
3. Port testing
4. HTTP requests
5. File transfer
6. Service banners
7. Timeouts
8. `ss` to observe the connection
9. Wireshark to inspect the packets

**Important:** only test systems you own or have explicit permission to test. Netcat itself is harmless, but unrestricted use against other systems can become unauthorized access or scanning.

If you're learning Netcat for cybersecurity, set up **two Kali/Ubuntu VMs on an isolated VMware network** and practice:

```text
Kali A                         Kali B
------                         ------
nc -lvnp 4444  <----------->  nc <IP-A> 4444
       TCP connection
```

Then practice **TCP/UDP connectivity, port testing, banners, HTTP requests, and file transfer**.
