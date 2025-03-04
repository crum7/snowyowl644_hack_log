---
{"dg-publish":true,"permalink":"/try-hack-me-memo/soc-level1/mitre/"}
---

- アメリカ合衆国に拠点を置く非営利の研究開発機関
### 主な概要

1. **設立と目的**
    
    - **設立年**: 1958年
    - **目的**: 国防、情報技術、サイバーセキュリティ、交通、医療など、多岐にわたる分野で政府や産業界に対する技術的支援とソリューションを提供すること。
2. **連邦研究開発センター（FFRDC）の運営**
    
    - MITREは複数の連邦研究開発センター（FFRDC）を運営しており、これにより米国政府のさまざまな機関に対して専門的な技術支援を提供しています。

### 主な活動
#### ATT&CKフレームワーク
-  (Adversarial Tactics, Techniques, and Common Knowledge) Framework
- 現実世界の監査rつに基づいた敵対者の戦術とテクニックに関する世界中からアクセス可能なナレッジベース
- 2013, MITREはAPTグループが企業のWindowsネットワークに対して使用する一般的なTTPを記録して文書化する必要性
#### CARナレッジベース
-   (Cyber Analytics Repository) Knowledge Base
- MITRE ATT&CK ® 攻撃者モデルに基づいて MITRE が開発した分析の知識ベース
- 攻撃検知と分析のためのモデル
	-  MITRE ATT&CK（Adversarial Tactics, Techniques, and Common Knowledge）フレームワークと連携し、特定の脅威や攻撃手法に対する分析方法を提供。
	- 攻撃者の行動やTTP（戦術、技術、手順）に基づいて分析を行う。
- フォーマット化されたデータ
- 検知ロジック
	- セキュリティツール（SIEM、EDR、NDRなど）やスクリプトで利用できる具体的な検知ロジックが含まれる。
- 実装ガイドライン
#### ENGAGE
- サイバーセキュリティにおける積極的な防御（Active Defense）のためのフレームワーク
- 敵対者への対応アプローチと考えられており、サイバー拒否 と サイバー欺瞞の実装によって実現される
- サイバー拒否
	- 敵対者が作戦を実行する能力を阻止
- サイバー欺瞞
	- 敵対者を欺くために意図的に偽の証拠や痕跡を残す
- Engage Web サイトでは、  Adversary Engagement Approach を「開始」するための[スターター キット](https://engage.mitre.org/starter-kit/)を提供している
	- スターター キットは、 開始するための さまざまなチェックリスト、方法論、 プロセス を説明するホワイト ペーパーと PDF のコレクション
	- https://engage.mitre.org/wp-content/uploads/2022/04/EngageHandbook-v1.0.pdf
- [Engage Matrix Explorer](https://engage.mitre.org/matrix)でわかりやすく見れる
- [MITRE ATT&CK](https://attack.mitre.org/)からの情報でフィルタリングできる
- MITRE Engageの3つのタブ（PREPARE, OPERATE, UNDERSTAND）
- PREPARE
	- 積極的防御を効果的に行うための準備をする段階
	- 目的
	    - 環境の整備と防御計画の作成。
	    - 攻撃者の動きを予測し、防御戦略を設計。
	- 主な活動
	    - デセプション（欺瞞）環境の構築（例: ダミーシステムや偽データの作成）。
	    - 脅威インテリジェンスの収集と分析。
	    - チームメンバーやツールの準備。
- OPERATE
	- 実際に積極的防御戦略を実行し、攻撃者の行動に直接対応するフェーズ
	- 目的
	    - 攻撃者を混乱させたり、行動を妨害したりする。
	    - 攻撃者の目的達成を阻止しながら、詳細な情報を収集。
	- 主な活動
	    - 攻撃者に対するリアルタイムの対応。
	    - 攻撃者を誘導するための操作。
	    - 偽情報の提供や侵入ルートの追跡。
- UNDERSTAND
	- - 攻撃者の行動や動機、使用技術を深く理解する段階です。
	- 目的
	    - 攻撃のパターンや技術を把握して、次の防御計画に役立てる。
	    - 組織全体のセキュリティ能力向上。
	- 主な活動
	    - 攻撃者から得られた情報の分析。
	    - サイバー攻撃キャンペーンの全体像の把握。
	    - 組織のセキュリティ体制の改善と強化。
- 具体的な使い方
	1. [マトリックス](https://engage.mitre.org/matrix/)から取り組む内容を決める
	2. [全てのツール](https://engage.mitre.org/tools/)で取り組む内容をサイト内検索で探す
	3. ツールがあった場合は、ツールを使って取り組む

#### D3FEND
- (Detection, Denial, and Disruption Framework Empowering Network Defense)
-  [D3FEND の](https://d3fend.mitre.org/)Web サイトによると、このリソースは「サイバーセキュリティ対策のナレッジ グラフ」
- D3FEND は、検出 、拒否 、および 中断フレームワーク 強化ネットワーク防御 
- この記事の執筆時点では、D3FEND マトリックスには 408 個のアーティファクトがある
- 具体的には
	- この手法とは何か (定義)
	- この手法がどのように機能するか (仕組み)
	- この手法を実装する際に考慮すべき事項 (考慮事項)
	- およびこの手法をどのように活用するか (例) 
- 
#### AEP
- (ATT&CK Emulation Plans)


##### 押さえておくべき言葉
- APT
	- Advanced Persistent Threat
	- 組織や国に対して[[TryHackMe_Memo/SOC_Level1/Cyber​​ Kill Chain\|Cyber​​ Kill Chain]]長期的な攻撃を行うチーム/グループ/国
	- Advancedは全てのAPTグループがセロデイエクスプロイトなどの超兵器を使用していると思いがちだが、そういうわけではない。
		- APTが単なるランダムなサイバー攻撃や短期的な侵入ではなく、計画的かつ洗練された手法を用いて、長期間にわたり標的に対して持続的な脅威を与えるものであるということ
- TTP
	- Tactics (戦術)、Techniques (技術)、Procedures (手順)

