# AI Workflow Scripts

Claude Code + Codex + Gemini を統合したAIワークフロースクリプト集。

## 概要

複数のAIを連携させ、コーディング・レビュー・修正を自動化するワークフローです。

```
Gemini(設計/分析) → Claude(実装) → Codex(レビュー) → Claude(修正) → ループ
```

## スクリプト一覧

| コマンド | 説明 | 用途 |
|----------|------|------|
| `/dev` | 実装モード | 新機能開発、バグ修正 |
| `/review` | レビューモード | 既存コードのレビュー＆修正 |
| `/codex-review` | Codex自動レビュー | バッチ処理による完全自動レビュー |

## スコアリング基準

80点以上で合格（自動ループ終了）

| カテゴリ | 配点 |
|---------|------|
| Security | 25点 |
| Correctness | 25点 |
| Performance | 20点 |
| Maintainability | 20点 |
| Testing | 10点 |
| **合計** | **100点** |

## インストール

```bash
# Claude Codeのcommandsディレクトリにコピー
cp commands/*.md ~/.claude/commands/
```

## 使用例

```bash
# 新機能を実装
/dev ユーザー認証機能を追加して

# 既存コードをレビュー
/review src/hooks/

# 全体レビュー
/codex-review
```

## ワークフロー詳細

### /dev（実装モード）

```
Step 0: Gemini設計・コンテキスト調査
Step 1: Claude実装
Step 2: Codexレビュー
Step 3: Gemini修正ヒント生成（80点未満の場合）
Step 4: Claude修正
→ Step 2に戻る（最大5回ループ）
Step 5: 完了
```

### /review（レビューモード）

```
Step 0: 対象の特定
Step 1: Geminiコンテキスト調査
Step 2: Codexレビュー
Step 3: Gemini修正ヒント生成（80点未満の場合）
Step 4: Claude修正
→ Step 2に戻る（最大5回ループ）
Step 5: 完了
```

### /codex-review（Codex自動レビュー）

- 3ファイルずつバッチ処理
- 各バッチ最大5回まで修正を試行
- 失敗バッチは理由と改善提案を出力

## ループ制御

| 条件 | アクション |
|------|-----------|
| 80点以上 | 完了 |
| 5回ループ | 強制終了 |
| 同スコア2回連続 | Gemini深掘り分析後、継続 |

## 2PC運用ガイド

複数PCで開発する場合の役割分担ルールは [WORKFLOW.md](./WORKFLOW.md) を参照。

| PC | 役割 | コマンド |
|----|------|---------|
| 開発PC | 開発 | `/dev` |
| レビューPC | レビュー | `/review` |

## 必要環境

- [Claude Code](https://claude.ai/code)
- [OpenAI Codex](https://platform.openai.com/docs/guides/code) または同等のレビューツール
- Gemini API（オプション：設計支援用）

## ライセンス

MIT
