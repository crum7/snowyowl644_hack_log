---
{"dg-publish":true,"permalink":"/honey-pot/t-pot-install-method/","noteIcon":""}
---

# T-Potについて
- HoneyPotをGCPで立てる
- HoneyPotとしては、一つで複数のハニーポットが立てられるT-Potを利用する
	- [🍯 T-Pot - The All In One Multi Honeypot Platform 🐝](https://github.com/telekom-security/tpotce/tree/8465b4e608906b4a8f9f2a22b983b7b900800bd1)
- 好きな場所にVMを置ける＆セキュリティの観点からも、GCPのCompute EngineのVMインスタンスを利用する
	- 国によって攻撃元の国が異なったりするのかなとか調べたい

# GCPの設定

## ファイアーウォールの設定
- 全てのポートを許可する設定をする
	- GCPは明示的にルールを書かないと解放されない
- VPCネットワーク→Firewall
	- ファイアーウォールルールを設定ボタン
		- 以下の画像の通りに設定
	- 管理者用画面、sshはログインかかる
		- 気になる人は64294、64295、64297番にファイアーウォールする
	- このルール以外は削除する
	- ![](https://raw.githubusercontent.com/crum7/Obsidian/main/HoneyPot/images/Pasted%20image%2020241231205736.png)

## Compute Engineの設定
- Compute Engine → VMインスタンス
	- VMインスタンス作成ボタン
	- T-PotのHiveというモードを使いたいので、要件に従って構成する
要件↓

| T-Pot Type | RAM  | Storage   | Description                                                                                 |
| ---------- | ---- | --------- | ------------------------------------------------------------------------------------------- |
| Hive       | 16GB | 256GB SSD | As a rule of thumb, the more honeypots, sensors & data, the more RAM and storage is needed. |
- e2-standard-4（4 vCPU、2 コア、16 GB メモリ）を使用する
- Debian GNU/Linux 12 (bookworm)
- ストレージ:256GB
- ネットワーキング→ネットワークタグ
	- default
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HoneyPot/images/Pasted%20image%2020241231213818.png)

作ったVMインスタンスにSSHで接続する
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HoneyPot/images/Pasted%20image%2020241231214445.png)


VMに接続して、T-Potをインストール
```
sudo apt update
env bash -c "$(curl -sL https://github.com/telekom-security/tpotce/raw/master/install.sh)"
```

install Typeは、一番多くのハニーポットが建てられるので、hを選択
```bash
### Choose your T-Pot type:
### (H)ive   - T-Pot Standard / HIVE installation.
###            Includes also everything you need for a distributed setup with sensors.
### (S)ensor - T-Pot Sensor installation.
###            Optimized for a distributed installation, without WebUI, Elasticsearch and Kibana.
### (L)LM    - T-Pot LLM installation.
###            Uses LLM based honeypots Beelzebub & Galah.
###            Requires Ollama (recommended) or ChatGPT subscription.
### M(i)ni   - T-Pot Mini installation.
###            Run 30+ honeypots with just a couple of honeypot daemons.
### (M)obile - T-Pot Mobile installation.
###            Includes everything to run T-Pot Mobile (available separately).
### (T)arpit - T-Pot Tarpit installation.
###            Feed data endlessly to attackers, bots and scanners.
###            Also runs a Denial of Service Honeypot (ddospot).

### Install Type? (h/s/l/i/m/t) h

### Installing T-Pot Standard / HIVE.

### T-Pot User Configuration ...
```

インストール完了したら、再起動する
```bash
sudo reboot
```

管理用のsshのポートが22番から、T-Potによって64295番に変更されてるので、「ブラウザウィンドウでカスタムポートを開く」で64295番を指定する
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HoneyPot/images/Pasted%20image%2020241231220134.png)

T-Potは再起動後に自動的に起動するので、上手くいっているかを確認する
```bash
sudo systemctl status tpot
```

T-Podを自分の環境で試したときは、activeになっているので上手くいっているかのように思われた。
しかし、[http://<VMの外部IP>:64297/]にアクセスできたり出来なかったりする事象が確認された。
これは、後述するが、GCEのDebianの仕様とT-Podの仕様が噛み合ってしまって、実際には正常に動いていないのに、systemctl status tpotではactiveになり、さも動いているかのように見えてしまうという事象である。

```bash
honeypot_trap_3@t-pot-2:~$ sudo systemctl status tpot
● tpot.service - T-Pot
     Loaded: loaded (/etc/systemd/system/tpot.service; enabled; preset: enabled)
     Active: active (running) since Tue 2024-12-31 13:07:43 UTC; 19s ago
    Process: 35168 ExecStartPre=/usr/bin/docker compose -f /home/honeypot_trap_3/tpotce/docker-compose.yml down -v (code=exited, status=0/SUCCESS)
   Main PID: 37368 (docker)
      Tasks: 20 (limit: 19175)
     Memory: 43.5M
        CPU: 1.459s
     CGroup: /system.slice/tpot.service
             ├─37368 /usr/bin/docker compose -f /home/honeypot_trap_3/tpotce/docker-compose.yml up
             └─37385 /usr/libexec/docker/cli-plugins/docker-compose compose -f /home/honeypot_trap_3/tpotce/docker-compose.yml up

Dec 31 13:08:00 t-pot-2 docker[37385]: tanner_redis         |   `-._    `-._`-.__.-'_.-'    _.-'
Dec 31 13:08:00 t-pot-2 docker[37385]: tanner_redis         |       `-._    `-.__.-'    _.-'
Dec 31 13:08:00 t-pot-2 docker[37385]: tanner_redis         |           `-._        _.-'
Dec 31 13:08:00 t-pot-2 docker[37385]: tanner_redis         |               `-.__.-'
Dec 31 13:08:00 t-pot-2 docker[37385]: tanner_redis         |
Dec 31 13:08:00 t-pot-2 docker[37385]: tanner_redis         | 1:M 31 Dec 2024 13:08:00.690 * Server initialized
Dec 31 13:08:00 t-pot-2 docker[37385]: tanner_redis         | 1:M 31 Dec 2024 13:08:00.690 * Ready to accept connections tcp
Dec 31 13:08:01 t-pot-2 docker[37385]: adbhoney             | INFO:config:Loading config from ./_internal/adbhoney.cfg
Dec 31 13:08:01 t-pot-2 docker[37385]: adbhoney             | INFO:ADBHoneypot:Configuration loaded with ['output_log', 'output_json'] as output plugins
Dec 31 13:08:01 t-pot-2 docker[37385]: adbhoney             | INFO:ADBHoneypot:Listening on 0.0.0.0:5555.
```


この事象を回避するための処理を行う。
問題なのは、T-Podは、上手くいかないとバックグラウンドでT-Podは再起動を繰り返すことである。
そのため、どこでエラーが起きているのかが、systemctlコマンドでは見つけにくいのであると思う。
まず、以下のコマンドで、T-Podのログを見る。
```bash
sudo journalctl -u tpot.service -f
```

しばらく見ていると、以下のようにメインプロセスで失敗し、コンテナが停止しているログが見つかる。
このエラーが起こる理由は、Debianの仕様で25番ポートを使用しており、T-Podも使用しようとしているためである。
```bash
Dec 31 13:10:03 t-pot-2 docker[44591]: Error response from daemon: driver failed programming external connectivity on endpoint mailoney (7303cd1b57e19c217baa8f83fdfe1879f2a71c3953cbaae9a411203df4980333): failed to bind port 0.0.0.0:25/tcp: Error starting userland proxy: listen tcp4 0.0.0.0:25: bind: address already in use
Dec 31 13:10:03 t-pot-2 systemd[1]: tpot.service: Main process exited, code=exited, status=1/FAILURE
Dec 31 13:10:03 t-pot-2 systemd[1]: tpot.service: Failed with result 'exit-code'.
```

今回は、T-Podを優先したいので、Debian側の25番ポートを使用しているプロセスを停止/パージさせる。
そのために、まずGCE側の25番ポートを使用しているプロセスを特定する。
lsofコマンドを使用する。
```bash
sudo apt install lsof
sudo lsof -i :25
```

自分の環境ではこのようになっていた。
```
honeypot_trap_3@t-pot-2:~$ sudo lsof -i :25
COMMAND PID        USER   FD   TYPE DEVICE SIZE/OFF NODE NAME
exim4   819 Debian-exim    4u  IPv4   2714      0t0  TCP localhost:smtp (LISTEN)
exim4   819 Debian-exim    5u  IPv6   2715      0t0  TCP localhost:smtp (LISTEN)
```

自分の環境では、exim4が25番ポートを使用していたため、exim4プロセスをストップさせる。
```bash
sudo systemctl stop exim4
sudo apt purge exim4 exim4-*

```

これで自分の環境は問題が解決した。
```bash
sudo systemctl restart tpot
```


[http://<VMの外部IP>:64297/]にアクセスすると、T-Podの管理画面にアクセスできる。
結構見ていると、T-Podにアクセス飛んできているの見れて楽しい。
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HoneyPot/images/Pasted%20image%2020241231230745.png)![](https://raw.githubusercontent.com/crum7/Obsidian/main/HoneyPot/images/Pasted%20image%2020241231231424.png)


## 30日間でデータが消えないようにするために
T-Pot → Kibana → # Stack Management → Index Lifecycle Policies
以下の写真の右の「Include managed system policies」ボタンをオンにする。
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HoneyPot/images/Pasted%20image%2020250101214023.png)

tpotを探してクリック
右下のManageボタンをクリック→Edit
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HoneyPot/images/Pasted%20image%2020250101214508.png)

画像のようにCold Phaseをオンにして、「Move data into phase when:30 days old」に設定
Save Policy
![](https://raw.githubusercontent.com/crum7/Obsidian/main/HoneyPot/images/Pasted%20image%2020250101214208.png)