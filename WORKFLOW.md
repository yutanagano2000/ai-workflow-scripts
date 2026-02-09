# 2PC運用ガイド

会社PCとMacの役割分担ルール。

## 役割分担

| PC | 役割 | コマンド |
|----|------|---------|
| 会社PC | 開発 | `/dev` |
| Mac | レビュー | `/review` `/codex-review` |

## 流れ

```
【会社PC】開発する
  git checkout -b feature/〇〇
  /dev 機能を実装して
  git push
  gh pr create

        ↓ PRが作られる

【Mac】レビューする
  gh pr checkout [PR番号]
  /review

  → 80点以上：承認してマージ
  → 80点未満：修正コメントを残す

        ↓ 修正が必要なら

【会社PC】修正する
  /dev 指摘を修正して
  git push

        ↓

【Mac】再レビュー
  /review
```

## 会社PCでやること

```bash
# 朝：最新を取得
git pull origin master

# 新機能を開発
git checkout -b feature/〇〇
/dev [タスク内容]

# 完了したらPR作成
git push origin feature/〇〇
gh pr create --base master
```

## Macでやること

```bash
# PRを確認
gh pr list

# PRをチェックアウトしてレビュー
gh pr checkout [PR番号]
/review

# OKならマージ
gh pr merge [PR番号]
```

## 基本ルール

1. **会社PC** = 開発に集中（`/dev`）
2. **Mac** = レビューに集中（`/review`）
3. masterへの直接コミットは禁止
4. 必ずPR経由でマージ

## スコア基準

| 結果 | アクション |
|------|-----------|
| 80点以上 | マージOK |
| 80点未満 | 修正依頼 → 会社PCで対応 |
