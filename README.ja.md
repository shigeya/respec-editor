# respec-editor

*[English](README.md) | 日本語*

W3C [ReSpec](https://respec.org/) 形式の HTML 仕様書を安全に編集し、プルリクエストを
準備するための [Claude Code](https://claude.com/claude-code) **スキル**です。

ReSpec は注釈付き HTML をロード時に W3C 風のスタイル付き仕様書へ変換するツールです。
その編集には、気づかぬうちに壊しやすい非自明な制約が数多くあります — 相互参照の
リンク切れ、ReSpec が自動生成するセクションの手書き、normative/informative 区分の
誤った切り替え、他の仕様から参照されている `id` の変更など。このスキルは、これらの
編集を ReSpec の作法に従って行い、相互参照の整合性を検証し、**不可逆な git / PR
操作の前には必ず確認のために停止する**よう Claude に指示します。

## できること

- ReSpec ソース HTML 内のセクション・定義（`<dfn>`）・normative 記述を、正しい
  セクションのネストと ReSpec マークアップを用いて追加・変更します。
- 相互参照を壊しません: 外部仕様への `data-cite`、内部の `<dfn>` ↔ `<a>` 用語リンク、
  `data-lt` の別表記、安定した `id`。
- 自動生成される成果物（目次、参考文献、定義の索引、セクション番号、後方参照）には
  手を加えません。
- フィーチャーブランチと差分を準備し、PR の下書きを作成したうえで、リモートへの
  ブランチ作成・push・PR 作成の前に**あなたの確認を待ちます**。

`respecConfig` のメタデータの変更、既存の `id` / `data-cite` キーの変更、
normative/informative 区分の切り替えは、あなたが明示的に依頼し、かつリポジトリ
自身の設定が許可している場合を除き、**あえて行いません**。

## 2層モデル

同じ共有スキルを多数の仕様リポジトリで使えるよう、スキルは2層に分かれています:

- **共有レイヤー（このリポジトリ）。** ReSpec 一般の知識を持ちます。特定の文書や
  リポジトリ固有の知識は一切含みません。
- **リポジトリ固有レイヤー。** 各仕様リポジトリのルートに置く `.respec-skill.md`
  というファイルで、仕様ごとに異なる事柄を保持します: どの外部仕様を `data-cite` で
  どのキーで参照するか、どの `respecConfig` フィールドが編集可能か、用語の規約、PR の
  規約など。通常は中央管理した設定へのシンボリックリンクで、git-ignore されるため、
  リポジトリ固有の情報が upstream にコミットされることはありません。

リポジトリ固有レイヤーの作成・リンク方法は [`SETUP.md`](SETUP.md) を、記入用の
テンプレートは [`templates/respec-context.template.md`](templates/respec-context.template.md)
を参照してください。

## インストール

ワークフローに合った方法を選んでください。

### マーケットプレイス経由のプラグイン（推奨）

Claude Code 上で:

```
/plugin marketplace add shigeya/respec-editor
/plugin install respec-editor@respec-skills
```

更新は `/plugin marketplace update respec-skills` で行います。この方法で
インストールした場合、スキルは `respec-editor:respec-editor` という名前空間に
なります。

### 個人スキルとして（すべてのプロジェクトで利用可能）

```bash
git clone https://github.com/shigeya/respec-editor.git \
  ~/.claude/skills/respec-editor
```

### プロジェクトスキルとして（1つのリポジトリに限定）

```bash
git clone https://github.com/shigeya/respec-editor.git \
  /path/to/your/project/.claude/skills/respec-editor
```

スキルを読み込ませるため、Claude Code を再起動（または新しいセッションを開始）して
ください。読み込まれたかは、利用可能なスキル一覧を Claude に尋ねるか、ReSpec 文書を
編集してみることで確認できます — このスキルは "ReSpec"、"respecConfig"、"dfn"、
"data-cite" といった語で起動するよう書かれています。

## 使い方

インストール後は、ReSpec 仕様の作業を Claude に依頼すると自動的に起動します。例:

> この設計議論を新しい normative セクションとして仕様に反映して。

> `index.html` に `digestSRI` の定義を追加して PR を準備して。

> セクション4の VC Data Model への `data-cite` 参照を修正して。

あるリポジトリで初めて使う際、スキルは `.respec-skill.md` を探します。存在しない
場合は、リポジトリの規約を推測せず、テンプレートから雛形を作成することを提案します。

## リポジトリ構成

```
SKILL.md                              スキル本体（実行時の指示）
.claude-plugin/plugin.json            プラグインマニフェスト（マーケットプレイス用）
.claude-plugin/marketplace.json       このプラグインを掲載するマーケットプレイス
SETUP.md                              運用者向けメモ: 中央保管＋シンボリックリンク設定
reference/respec-markup.md            ReSpec マークアップ規則（section, dfn, data-cite ...）
reference/respec-config.md            respecConfig フィールドと編集可否の区分
reference/pr-workflow.md              ブランチ・差分・PR・確認ゲート
templates/respec-context.template.md  リポジトリの .respec-skill.md 用テンプレート
examples/sample-section.html          最小の ReSpec ソース断片
```

## 対象範囲

ReSpec HTML 専用です。ReSpec でない HTML、[Bikeshed](https://tabatkins.github.io/bikeshed/)
ソース（`.bs`）、一般的な Markdown には使用しません。

## ライセンス

[MIT](LICENSE) © Shigeya Suzuki
