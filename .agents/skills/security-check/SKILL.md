---
name: security-check
description: コミット前やリリース前に、CLIツールを活用してトークン消費を最小化しながらCVSS High/Critical（7.0以上）の即死脆弱性を検出し、ピンポイントで修正するスキル。未導入ツールはmac/win環境に応じて自己調達する。
---

# セキュリティチェックスキル (Security-Check Protocol)

> **哲学**: "Scan with CLI, Fix with AI."（検査はCLIでトークンゼロ、修正のみAIがピンポイントで行う）

本スキルは、セキュリティ検査（PHASE_4）時やリリース前、または人間から「セキュリティチェックして」「脆弱性を見て」と指示された際にロードされる。全ファイルをAIに読ませるトークン浪費を厳禁とし、ローカルCLIによる高速スキャンを強制する。

---

## 1. 検査の3大フォーカス（CVSS 7.0+ High/Critical）
本スキルでは、システム停止や情報漏洩に直結する **CVSS 7.0以上（High / Critical）のみをブロック対象** とする。Medium / Low は警告にとどめ、開発速度を落とさない。

1. **シークレット漏洩 (Secret Leaks)**: APIキー、秘密鍵、DB認証情報のコード内ハードコード。
2. **既知のパッケージ脆弱性 (SCA)**: 依存関係に含まれる重大なCVE（リモートコード実行等）。
3. **重大なコード脆弱性 (SAST)**: 生SQL結合（SQLi）、未エスケープ出力（XSS）、パストラバーサル等。

---

## 2. ツールの自己調達原則 (Auto-Provisioning: macOS / Windows 両対応)

> ⚠️ **「ツールが入っていないから省略・簡易検査で済ませる」は禁止。**  
> `ponytail` でテスト環境を自己構築するのと同様に、セキュリティCLIツールが存在しない場合は**AI自らがOS（macOS / Windows）を判定し、直ちにインストール・調達を試みること**。

### 自己調達コマンドの標準フロー

| 検査種別 | 標準ツール | macOS (Homebrew) | Windows (winget / scoop / choco) | 共通 (言語マネージャ) |
| :--- | :--- | :--- | :--- | :--- |
| **シークレット** | **Gitleaks** | `brew install gitleaks` | `winget install Gitleaks.Gitleaks` または `scoop install gitleaks` | `go install github.com/gitleaks/gitleaks/v8@latest` |
| **依存関係 (JS/TS)** | **npm audit** | 標準装備 | 標準装備 | `npm audit` / `pnpm audit` |
| **依存関係 (Python)** | **pip-audit** | `pip install pip-audit` | `pip install pip-audit` | `uv tool run pip-audit` |
| **依存関係 (汎用)** | **Trivy** | `brew install trivy` | `winget install AquaSecurity.Trivy` または `scoop install trivy` | インストーラスクリプト |
| **静的解析 (SAST)** | **Semgrep** | `brew install semgrep` | `pip install semgrep` | `pip install semgrep` |

> 🛡️ **Spike撤退リミッターの適用**:  
> 管理者権限不足やネットワーク制限等で**インストールに2回連続失敗した場合のみ**深追いせず即座に撤退し、簡易フォールバック（言語標準Linterやgrep検査）に切り替える。その際、理由は必ず `docs/decision-log.md`（ADR）に1行記録すること。

---

## 3. 実行手順（3ステップ・スキャン）

ツールを準備した上で、AIは以下のコマンドを順次実行し、**終了ステータスと数行の要約のみ** を確認する（全ログの読み込みは禁止）：

### Step 1: シークレット検査
```bash
gitleaks detect --no-git --verbose
```
※ Spike撤退時のみ: `git status` で `.env` や秘密鍵がステージングされていないか確認。

### Step 2: 依存関係の脆弱性検査
プロジェクトの環境に応じたコマンドを実行（High/Criticalのみ抽出）：
- **Node.js**: `npm audit --audit-level=high` または `pnpm audit --audit-level=high`（標準装備）
- **Python**: `pip-audit --desc`
- **Go / Rust / 汎用**: `trivy fs --severity HIGH,CRITICAL .` または `cargo audit`

### Step 3: 静的コード解析 (SAST)
```bash
semgrep scan --config auto --severity ERROR
# または
trivy fs --severity HIGH,CRITICAL --security-checks config,vuln .
```
※ Spike撤退時のみ: プロジェクト既存のLinter（`eslint`, `flake8` 等）で代行。

---

## 4. 判定と対処プロトコル

### A. High / Critical が「0件」の場合
直ちに以下の形式で報告し、検査を完了する（合格）：
```text
✅ セキュリティチェック合格（CVSS High/Critical: 0件）
- シークレット漏洩: なし (Gitleaks)
- 依存関係脆弱性: なし (npm audit / pip-audit / Trivy)
- 重大コード脆弱性: なし (Semgrep / Trivy)
```

### B. High / Critical が「検出」された場合
1. **ピンポイント取得**: 警告が出た「該当ファイル」の「該当行（前後10〜20行程度）」のみを `view_file` で閲覧する（**ファイル丸読みは厳禁**）。
2. **最小修正の適用**:
   - シークレット: 環境変数への外出しと `.gitignore` への追記。
   - パッケージ: 該当パッケージの安全なバージョンへの更新。
   - コード脆弱性: プレースホルダ化、エスケープ処理の追加。
3. **再スキャン**: 修正後、再度コマンドを実行してエラーが解消したことを確認（Run & Verify）して報告する。
