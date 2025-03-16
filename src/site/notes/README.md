---
{"dg-publish":true,"permalink":"/readme/","tags":["gardenEntry"],"noteIcon":""}
---


本サイトは、**Obsidian** を活用してペネトレーションテスト（Pentest）やセキュリティ関連の知識を効率的に整理・管理するためのものです。
HackTheBoxやTryHackMeでの学習メモ、チートシートなど、実践的な情報が中心です。

---

**📂 ディレクトリ構成**

```
obsidian-pentest/
├── Defence_Technique          # 防御に関する技術情報やメモ
├── HackTheBox_Writeups        # HackTheBoxで取り組んだマシン・ChallengeのWriteup
│   └── Retired
│       ├── Easy
│       └── Medium
├── Hack_The_Box_Academy       # HackTheBox Academyのメモや知見
├── HoneyPot                   # T-Potなどのハニーポット関連の導入・分析メモ
├── Pentest_Technique          # ペネトレーションテストに関する技術的チートシートや手法メモ
├── Pentest_Theory             # ペネトレーションテストに関する理論的整理や概念
├── Template                   # Writeup用のMarkdownテンプレートなど
├── TryHackMe_Memo             # TryHackMeでの演習・学習メモ
├── イベント・プロジェクトレポ    # 参加したイベントやプロジェクトのレポート
├── セキュリティ基礎               # セキュリティの基本概念や各種手法・脆弱性解説
├── 非公開                      # 個人用メモや機密度の高い情報（外部公開は想定していない）
├── _config.yml                # （Obsidianやサイト生成用などに利用する場合）
├── README.md                  # 本ファイル
```

**フォルダ概要**

• **Defence_Technique/**

防御側の技術やセキュリティソリューションに関するメモ。

• **HackTheBox_Writeups/**

• Retired Machine（Easy, Mediumなど）ごとにサブフォルダを分け、実際の手法や気づき、流れをそのまま書いているWriteup集です。

• 「解法手順のお手本」としてのWriteupというよりは、**学習の足跡**や**調査ログ**として扱います。

• **Hack_The_Box_Academy/**

HackTheBox Academyのコースやラボに関するメモを管理します。

• **HoneyPot/**

T-Potなどハニーポットのインストール方法から分析手法まで、導入〜運用時の知見をまとめています。

• **Pentest_Technique/**

侵入テスト時に役立つテクニックや小技、チートシート的な情報。

例: ポートフォワーディング、ファイル転送、Linux/Windows向けパスワードクラック手法など。

• **Pentest_Theory/**

Enumeration（情報収集）や攻撃プロセスの流れなど、ペネトレーションテスト全般に関する理論的・体系的な整理。

• **Template/**

HackTheBoxやTryHackMeのWriteupを書くときのMarkdownテンプレートなど、ドキュメント作成時のひな形を置いています。

• **TryHackMe_Memo/**

TryHackMeで学んだ内容のメモ、演習問題やRoom攻略のポイントなど。

• **イベント・プロジェクトレポ/**

カンファレンスや勉強会、開発プロジェクト参加時のレポートや成果をまとめています。

• **セキュリティ基礎/**

各種攻撃手法（XSS, CSRF, SSRFなど）の基本解説や、Firewall/IDS/IPS・Linux/Windows基礎などを記載。

• **非公開/**

外部には公開しない個人的なメモ（SecHack365の進捗など）や申込情報を置いています。

---

**🚀 活用方法**

1. **Obsidian** で本リポジトリを開き、ノートを閲覧・編集

• 双方向リンクやタグ機能を活用することで、複数の手法・メモを横断的に管理できます。

2. **学習ログとしてのWriteup**

• HackTheBoxやTryHackMeのWriteupは、フラグ奪取だけでなく試行錯誤のプロセスを含め記録することで、後からの振り返りが容易になります。

3. **防御から攻撃まで一元管理**

• Defence_Technique と Pentest_Technique などを両方閲覧し、「攻撃手法に対してどう防ぐか」を意識して学習できます。

4. **プロジェクトレポ・イベントレポ**

• 過去参加したイベント・学会を集約し、管理します。

---

**参考リンク**

• [Scrapbox（旧メモ置き場）](https://scrapbox.io/tadanomemo777/)

旧メモを管理しているスクラップボックス。現状は**こちらのサイトの情報が最新**です。

---

以上が本リポジトリの概要です。何かご不明点や改善提案があれば、IssueやPull Requestで気軽にお知らせください。今後も適宜アップデートしていきます。