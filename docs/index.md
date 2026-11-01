# プロジェクトドキュメントインデックス

> **方針**: 巨大な手動リストを廃止し、必要最低限のコア文書のみを管理する。
> コードや文書の依存関係・全体像は `.agents/skills/graphify`（ナレッジグラフ）により自動追従する。

---

## コア設計文書（必要に応じて作成）

| 書類名 | 用途 | ステータス |
| :--- | :--- | :--- |
| [CODING_RULES.md](file:///Users/abekoudai/Desktop/dev_md/docs/CODING_RULES.md) | 実装規約（Ponytail 7段階ラダー・テスト義務化） | 確定 |
| [DEV_POLICY.md](file:///Users/abekoudai/Desktop/dev_md/docs/DEV_POLICY.md) | 協働プロトコル（対話ブレーキ・自律推進） | 確定 |
| [decision-log.md](file:///Users/abekoudai/Desktop/dev_md/docs/decision-log.md) | ADR（技術選定・Spike撤退記録） | 確定 |
| `architecture.md` | システム全体構成・主要データフロー | 必要時作成 |
| `api-spec.md` | エンドポイント・インターフェース仕様 | 必要時作成 |

---

## 探索と参照ルール
- 個別の関数や詳細な依存関係は、手動のMarkdown一覧ではなく、Graphify（`graphify-out/GRAPH_REPORT.md`）を参照すること。
