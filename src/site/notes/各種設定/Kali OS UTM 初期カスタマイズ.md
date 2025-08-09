---
{"dg-publish":true,"permalink":"//kali-os-utm/","noteIcon":""}
---


Kali UTM ファイル
- https://mac.getutm.app/gallery/kali-2023
# UTM側の設定
## ネットワーク
UTMの共有ネットワークは、NATなので、普通の場合はNATのままで良い
触らない
## ディスプレイ
- ハードウェア
	- virtio-ramfb
- ディスプレイサイズをウィンドウサイズに自動的に合わせる
	- ポンコツなのでオフ
	- 必ずチェックを外す
		- ディスプレイのサイズの変更はParrot OS側でいじったほうが良い
- 拡大縮小
	- なんのことかよく分かってないけど両方ともリニア
- Retinaモード
	- 上記と同様だが、オン
# Kali OS側の設定
GUIはXFCEね
## windowsキー押した時に出てくるショートカットを消す
- mac使ってると、kali OS使ってるときに英語にしようとして、command(windowsキー）を押して結構ウザいのでショートカットを消す
```sh
xfconf-query -c xfce4-keyboard-shortcuts -r -p "/commands/custom/<Primary>Escape"
```

## ctrlとcommandを入れ替える
- mac、commandしか使わないのに、parrot osではcommandばっか使うんのなんて嫌すぎ！
```sh
setxkbmap -option ctrl:swap_lwin_lctl
```

## 解像度設定
5120❌1440の3分割のモニターで、綺麗に1/3にするための設定
```sh
cvt 1780 1440
xrandr --newmode "1784x1440_alt" 217.00 1784 1912 2104 2424 1440 1443 1453 1493 -hsync +vsync
xrandr --addmode Virtual-1 "1784x1440_alt"
xrandr --output Virtual-1 --mode "1784x1440_alt"
```

CA Tech Loungのモニターで、(3360❌1890)で2分割にするための設定
```sh
cvt 1680 1890
xrandr --newmode "1680x1890_60.00"  270.50  1680 1808 1992 2304  1890 1893 1903 1958 -hsync +vsync
xrandr --addmode Virtual-1 "1680x1890_60.00"
xrandr --output Virtual-1 --mode "1680x1890_60.00"
```

macで2分割
```sh
cvt 900 1169
xrandr --newmode "904x1169_60.00"   87.75  904 960 1056 1208  1169 1172 1182 1212 -hsync +vsync
xrandr --addmode Virtual-1 "904x1169_60.00"
xrandr --output Virtual-1 --mode "904x1169_60.00"
```
## パッケージアップグレード
```sh
sudo apt upgrade
sudo apt update 
```
## Bloodhound
admin : B7r!kLp29#qW
admin : mimic


## ログインしたらやるコマンド

```sh
setxkbmap -option ctrl:swap_lwin_lctl
xrandr --output Virtual-1 --mode "1784x1440_alt"
```


ヒントの有効化
```sh
unset ZSH_AUTOSUGGEST_DISABLE
source ~/.zshrc
```


ヒントの無効化
```sh
ZSH_AUTOSUGGEST_DISABLE=1
source ~/.zshrc
```
# 環境構築
新しいツールを入れたら、次のためにパスを通す
ダウンロード・インストールフォルダ : `/home/kali/tools`
エイリアスを書くフォルダ : `/home/kali/.local/bin`

pythonの場合 
- 仮想環境を作成
```sh
python3 -m venv venv
source venv/bin/activate
```

エイリアス作成
```sh
cd /home/kali/.local/bin
nano <ToolName>
```

書き込み
```sh
#!/bin/bash
source ~/tools/<ToolName>/venv/bin/activate
python3 ~/tools/<ToolName>/<Excute File> "$@"
```

```sh
chmod +x ~/.local/bin/<ToolName>
```

# ショートカットの設定
autohotkeyの設定
![](https://i.imgur.com/p5gT7We.png)
