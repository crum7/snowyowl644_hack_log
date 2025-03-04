---
{"dg-publish":true,"permalink":"/readme/","tags":["gardenEntry"]}
---


このリポジトリは、**Obsidian**を活用してペネトレーションテスト（Pentest）やセキュリティ関連の知識を効率的に管理するためのものです。  
HackTheBoxやTryHackMeの学習メモ、チートシートなどを中心に構成されています。

---

## 📖 内容

このリポジトリには以下のフォルダが含まれています

- **`HackTheBox_Writeup/`**  
    HackTheBoxで取り組んだマシンやチャレンジのWriteup（解答・メモ）を管理します。
    フラグまでの最短経路の綺麗なwriteupではなく、自分が実際に行なっていること、気づき、流れをそのまま書いています。
- **`TryHackMe_Memo/`**  
    TryHackMeで学んだ内容やチャレンジのメモを保存します。
    
- **`Pentest_Cheat_Sheet/`**  
    ペネトレーションテストやセキュリティ関連の便利なチートシートをまとめています。  
    例: コマンドリスト、ツールの使い方、脆弱性の調査方法など。
    
## 前回まで
- 一部,旧scrapboxに情報はありますが、こちらの方がバージョンは新しいです。
- https://scrapbox.io/tadanomemo777/
---

## 📂 ディレクトリ構成

```plaintext
obsidian-pentest/
├── HackTheBox_Writeup/    # HackTheBoxのWriteup
├── TryHackMe_Memo/        # TryHackMeのメモ
├── Pentest_Cheat_Sheet/   # チートシートや便利メモ
└── README.md              # このドキュメント
```

---


# 画像リンク変換スクリプト
upload_images_to_imgur.py

このスクリプトは、**Obsidian 独自の画像埋め込み記法** ( `![[image.png]]` ) や **ローカルパス指定** ( `![](images/image.png)` ) を、**GitHub 上でも正しく表示される `raw.githubusercontent.com` 形式**に変換する Python スクリプトです。

## 使い方

1. リポジトリをクローンし、スクリプト (`replace_image_links.py`) をリポジトリ直下に配置  
2. スクリプトを実行  
   ```bash
   python3 replace_image_links.py
   ```
3. 変換が実行され、Markdown ファイル内の画像リンクが修正されます  
4. 修正内容を確認し、問題なければコミット & プッシュしてください  

## 注意点

- スクリプトは `.md` ファイルを対象に再帰的に検索します  
- 画像パスが `images/` フォルダ以外にある場合は、スクリプト内のパス生成ロジックを調整してください  
- まれに壊れた URL (二重 URL など) がある場合、正規表現で補正できないものは手動で修正が必要です  

---