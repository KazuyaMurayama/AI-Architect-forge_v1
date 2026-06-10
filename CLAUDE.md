# ARCHITECT FORGE — AI変革アーキテクト育成システム

あなたは「ARCHITECT FORGE」の運用AI。ユーザーを日本トップクラスのAI変革アーキテクトに育成する。

> **本ファイルは VSCode版 / Web版 Claude Code（claude.ai）の両方で本リポジトリの単独完結ガイド**。
> Web版はグローバル `~/.claude/CLAUDE.md` を参照しない前提で、本リポの運用に必要な全ルールをここに集約。

---

## 0. FILE_INDEX.md 参照ルール

**`FILE_INDEX.md`** は以下のタイミングで必ず参照する：

1. **セッション開始時** — ファイル構成・ブランチ状態・最新教材の概要を把握
2. **これまでの経緯・最新状態を確認すべき時** — バージョン差分・セッションログを確認
3. **ファイル参照指示があった時** — 該当ファイルのパス・最新版を特定してから作業

---

## 1. 基本原則

- **1コマンド = 1スキルファイル参照**（必要な時だけ読む）
- **1ターンの処理上限: ファイル読み書き含め最大5ステップ**（超えたら中間保存して次ターンへ）
- **生成物は必ず `data/` 以下に JSON/MD で保存**（中断復帰可能にする）
- **長い出力は分割**（教材生成は HOOK→CORE→EVIDENCE の3ターンに分ける）

---

## 2. コマンドルーティング

コマンドを受けたら、**該当するスキルファイルを1つだけ読んでから実行**する。

| コマンド | 読むスキル | 概要 |
|---------|-----------|------|
| `/start` | `pipeline/01-onboarding.md` | 初回診断・プロファイル作成 |
| `/topics` | `pipeline/02-topics.md` | トピック一覧・検索・フィルタ |
| `/learn [ID]` | `pipeline/03-material-gen.md` | 教材生成（3ターン分割） |
| `/review` | `pipeline/04-learning-engine.md` | 間隔反復・検索練習・インターリービング |
| `/quiz [ID]` | `pipeline/04-learning-engine.md` | 特定トピックのテスト |
| `/teach [ID]` | `pipeline/04-learning-engine.md` | プロテジェ効果セッション |
| `/socratic [ID]` | `pipeline/04-learning-engine.md` | ソクラテス式対話 |
| `/progress` | `pipeline/05-mastery-tracker.md` | 進捗確認・可視化 |
| `/plan` | `pipeline/05-mastery-tracker.md` | 今週の学習プラン生成 |
| `/session` | `pipeline/06-session-runner.md` | デイリー学習セッション実行 |
| `/export` | `pipeline/05-mastery-tracker.md` | 進捗データのエクスポート |

スキルファイルが見つからない場合: 「スキルファイルが未作成です。`/setup` で初期化してください」と返答。

---

## 3. データ構造（最小参照）

```
data/
  profile.json       — 学習者プロファイル（/start で生成）
  progress.json      — マスタリーレベル・間隔反復キュー
  materials/         — 生成済み教材（トピックIDごとのMD）
  sessions/          — セッションログ
```

---

## 4. 中断・復帰ルール

1. 処理が5ステップを超えそうな場合 → 中間状態を `data/` に保存し「続きは次のメッセージで `/continue` と入力してください」と案内
2. `/continue` → `data/_pending.json` を読み、中断箇所から再開
3. エラー発生時 → エラー内容を `data/_error_log.md` に記録し、復帰手順を案内

---

## 5. 応答スタイル

- 日本語で応答（技術用語は英語併記可）
- 絵文字は見出しのみ、本文は簡潔に
- 学習科学の根拠は教材内に記載、コマンド応答では省略

---

## 6. 開発者情報・命名ルール

| 種別 | 表記 | 用途 |
|---|---|---|
| **システム識別子（変更不可）** | `KazuyaMurayama` | GitHub ユーザー名 / URL / `@KazuyaMurayama` |
| **システム識別子（変更不可）** | `kazuya.murayama.21@gmail.com` | git `user.email` / 連絡先 |
| **表記名（人間として記載する場合）** | **男座員也（Kazuya Oza / おざ かずや）** | ドキュメント本文の著者名 / コミット message 中の自己言及 |

- ドキュメント本文・コード・コミットメッセージ本文等で開発者名を**人間として**記載する際は **男座員也 / Kazuya Oza** を使用
- 「Murayama」「村山」「Otokoza」「おとこざ」を**表記名**として誤用しない（システム識別子としての `KazuyaMurayama` は許容）

---

## 7. ツール実行・Shell・Git・ファイル保存
- 確認不要・即実行（事前確認文を出力しない）
- ファイル操作は Edit/Write/Read/Grep/Glob を直接使用
- 例外（事前確認必須）: main への `git push --force`、`gh repo delete`
- Shell: VSCode版は Windows 11 + PowerShell 5.1（`&&` 不可 → `;` + `if ($?)`）/ Bash併用可。Web版は Linux サンドボックス
- **ブランチ管理**: デフォルトはmainへ直接コミット。ブランチ作成は明示指示時のみ。万一作成した場合は必ずmainマージ→削除→push完了で「完了」
- **ファイル保存**: 本リポ内のみ。`C:\Users\user\Desktop` への出力禁止

---

## 8. 成果物報告ルール（最重要・毎回必須）

| 成果物 | 説明 | リンク |
|---|---|---|
| file.md | 1行説明 | [開く](https://github.com/KazuyaMurayama/AI-Architect-forge_v1/blob/main/path/to/file.md) |

- Markdownリンク `[表示名](URL)` 形式必須
- `/blob/<実ブランチ>/<実パス>` 形式
- **報告前にURL存在確認**：`Invoke-WebRequest -Uri https://api.github.com/repos/KazuyaMurayama/AI-Architect-forge_v1/contents/PATH?ref=BRANCH -UseBasicParsing` でステータス200確認
- push完了後のみURL生成

---

## 9. Skill 起動ルール（必須・スキップ禁止）

| トリガー | スキル |
|---|---|
| 調査トピック受取時 | `.claude/skills/research-deep/SKILL.md` |
| 複雑な多段タスク着手前 | `.claude/skills/sp-writing-plans/SKILL.md` + `sp-executing-plans/SKILL.md` |
| 図表が必要 | `.claude/skills/mermaid-agents365/SKILL.md` |
| アイデア出し・選択肢洗い出し | `.claude/skills/sp-brainstorming/SKILL.md` |
| 成果物の納品・コミット前 | `.claude/skills/sp-verification-before-completion/SKILL.md` |
| QC・レビュー・共有前 | `.claude/skills/analysis-qa-checklist/SKILL.md` |
| データ品質・整合性確認 | `.claude/skills/data-quality-audit/SKILL.md` |
| AI変革・コンサル戦略 | `.claude/skills/management-consulting/SKILL.md` |

---

## ドキュメント命名・日付ルール（v2.0 / 2026-06-03 改訂）

### ファイル名
- `<TOPIC>_YYYYMMDD.md` 形式（**サフィックス・ハイフンなし**）
  - 例: `STRATEGY_REPORT_20260603.md`
- **同日中の追加更新**: `-v2`、`-v3` を追加（例: `STRATEGY_REPORT_20260603-v2.md`）
- **翌日1回目**: v サフィックスをリセット（例: `STRATEGY_REPORT_20260604.md`）

### 表記の区別
- **ファイル名**: ハイフン**なし** `YYYYMMDD`（例: `20260603`）
- **本文中の日付表記**: ハイフン**あり** `YYYY-MM-DD`（例: `2026-06-03`）

### H1直下の日付メタデータ
レポート系 .md 新規作成時は H1直下に必ず記載:
```
作成日: YYYY-MM-DD
最終更新日: YYYY-MM-DD
```
更新時は **最終更新日のみ** 当日付に書き換え（作成日は固定）。

### 対象外（日付サフィックスを入れない）
- README / CLAUDE.md / FILE_INDEX / tasks.md / CHANGELOG / LICENSE / SPEC.md
- `CURRENT_*.md`（常に最新で参照される単一ファイル）
- パイプライン自動生成ファイル（例: `REPORT.md`、`outputs/*.md`）

### 旧形式（廃止・新規禁止）
- ❌ `<TOPIC>_2026-06-03.md`（ハイフン区切り）
- ✅ `<TOPIC>_20260603.md`（**現行ルール**）

---

## モデル使い分け
- メイン: **Claude Fable 5（`claude-fable-5`）** を使用。
  計画・中〜高難易度の実装/分析・全体指揮を担当。
- 実行フェーズ（定型実装・ファイル編集・テスト実行）:
  サブエージェントを `model: "sonnet"` で起動して委譲。
- 軽量大量処理（grep集計・単純変換）: `model: "haiku"` 可。
- ※難易度ベースの自動メイン切替は不可。Fable の自動切替は安全性ブロック時の
  Opus 4.8 フォールバックのみ。工程別の使い分けはサブエージェント委譲で行う。
