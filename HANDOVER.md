# HANDOVER.md: dev_md 自動引き継ぎ・セットアッププロトコル

> 🚨 **AIエージェントへの即時実行指令 (IMMEDIATE EXECUTION DIRECTIVE)**:
> **あなたがこのファイル（`HANDOVER.md`）を読み込んだ、またはユーザーから提示・メンションされた場合、質問や事前確認（「実行しますか？」など）は一切行わず、直ちに以下の手順をツールを用いて最後まで全自動で実行してください。**  
> 会話での返答は、すべてのファイル配置・ゴミ掃除・検証が完了した後の「最終完了報告」のみとします。

---

## 👤 人間（ユーザー）向けクイックスタート

導入したいプロジェクトのルートディレクトリで、以下を実行してAIに渡すだけです：

```bash
# 1. 一時クローンする
git clone --depth 1 https://github.com/kodai-n11qbb/dev_md.git .tmp_dev_md
```

**AIへの指示（これだけで完了）：**
> `@.tmp_dev_md/HANDOVER.md`
> （または「`.tmp_dev_md/HANDOVER.md` を読んで」）

AIが自動的に引き継ぎ処理を開始し、終了報告まで完全自動で完遂します。

---

## 🤖 AIエージェントの自律実行手順（Fully Automated Steps）

AIは以下のステップを**1つのターン内で中断せず、自律的にコマンドやファイルツールを実行して完了**させてください。

### Step 1: 資産の配置（Target File Deployment）
導入元（`.tmp_dev_md/` または本ファイルが存在するディレクトリ）から、プロジェクトルートへ以下をコピー/配置する：
1. `AGENTS.md` → プロジェクトルート（`.`）
2. `.agents/` ディレクトリ一式 → プロジェクトルート（`.agents/`）
3. `docs/` ディレクトリ一式 → プロジェクトルート（`docs/`）
   - ※ すでにプロジェクトに `docs/` がある場合は上書きせず、`docs/_meta_guide.md`、`docs/decision-log.md`、および未作成の場合の `docs/concept.md` をマージ・追加する。

> ⚠️ **禁止事項**: 導入元の `.git/`、`.gitignore`、`README.md`、`HANDOVER.md` などのプロジェクト固有ファイルをプロジェクトルートに混入・上書きしてはならない。

### Step 2: ゴミ掃除とGit汚染防止（Clean Up）
1. 一時クローンディレクトリ（`.tmp_dev_md/`）を完全に削除する：
   ```bash
   rm -rf .tmp_dev_md
   ```
2. プロジェクトルートの `.gitignore` を確認し、ナレッジグラフ出力ディレクトリ（`graphify-out/`）が含まれていなければ末尾に追記する：
   ```gitignore
   # dev_md knowledge graph
   graphify-out/
   ```

### Step 3: プロジェクト環境の自動判定（Environment Detection）
プロジェクトルートのファイル構成を調査し、以下を判定・把握する：
- **主要言語/フレームワーク**:（例: Node.js/TypeScript, Python, Go, Rust 等）
- **テスト環境**:（例: Vitest, Jest, pytest, go test が存在するか否か）
  - ※ テスト環境がない場合、後続の実装（PHASE_3: ponytail）に備えて「未導入」と把握しておく。

### Step 4: プロジェクトコンセプトの自動抽出・生成（Concept Ingestion）
プロジェクトルートの既存資産（`README.md`、`package.json` の name / description、主要コード等）をスキャンする：
- **既存プロジェクトの場合**: スキャン結果から「解決する課題 (Why)」「想定ユーザー (Who)」「コア機能 (In-Scope)」を抽出し、`docs/concept.md` を自動起票する。
- **新規・空プロジェクトの場合**: 最小限の骨子テンプレート（Why / Who / Scope）を `docs/concept.md` に配置し、完了報告で「プロジェクトの目的を1言で固定しましょう」と促す。

### Step 5: 最終完了報告（DoD達成）
すべてのツール実行が完了したら、以下のフォーマットで人間に報告して終了する：

```text
🎉 dev_md の自動セットアップが完了しました！

【配置・更新された資産】
- AGENTS.md : エージェント自動認識エントリーポイント
- .agents/skills/ : オンデマンドスキル群（goal-setting, graphify, ponytail, security-check）
- docs/ : プロジェクト北極星（concept.md）、保守ガイド（_meta_guide.md）、ADR（decision-log.md）
- .gitignore : graphify-out/ を追加

【検出されたプロジェクト環境】
- 主要言語: <検出された言語>
- テスト環境: <テストランナー名 または 未導入>
- プロジェクト目的 (concept.md): <自動抽出した1行要約 または 「未固定（何を作るプロジェクトか1言教えてください）」>

【開発の始め方】
- 機能を作りたい時: 「〜を作りたい」「〜を実装して」（PHASE_1a: 目的設定・concept照合が起動）
- 軽微な修正・バグ対応: そのまま修正内容を指示（PHASE_0が起動）
- セキュリティ検査: 「セキュリティチェックして」（PHASE_4: CLI高速スキャンが起動）
- ルールを見直したい時: 「ルールを見直して」（保守プロトコルが起動）
```
