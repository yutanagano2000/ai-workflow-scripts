# Codex Auto Review & Fix

コードベースの完全自動レビュー＆修正ループを実行します。

## トリガー
- `/codex-review` コマンド
- 「レビュー開始」「コードレビュー」「Codexレビュー」などの発言

## ワークフロー

以下の自動ループを実行してください：

### 1. 初期化
```bash
~/.claude/hooks/codex-integrated-loop.sh "$(pwd)" init
```

### 2. 各バッチを処理（最大5イテレーション）

```
for batch_num in 1..total_batches:
  iteration = 1

  while iteration <= 5:
    # レビュー実行
    result = ~/.claude/hooks/codex-review-batch.sh "$(pwd)" $batch_num

    # スコア抽出
    if score >= 80:
      echo "✅ Batch $batch_num PASS"
      break

    # 80点未満: 問題を修正
    for each issue in result:
      - Read the file
      - Apply the fix using Edit tool
      - Verify the fix

    iteration++

  if score < 80 after 5 iterations:
    # 失敗リストに追加
    failed_batches.append({
      batch: batch_num,
      final_score: score,
      remaining_issues: issues,
      reason: "Max iterations reached"
    })
```

### 3. 最終レポート

処理完了後、以下を出力：

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Codex Review Complete
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Total Batches: X
  Passed: X (80点以上)
  Failed: X (80点未満)
  Pass Rate: X%
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[失敗バッチがある場合]
━━━ Failed Batches ━━━
Batch X: Score Y/100
  Files: file1.ts, file2.ts
  Reason: Max iterations reached
  Remaining Issues:
    - [CRITICAL] file:line - description
    - [HIGH] file:line - description

━━━ Improvement Recommendations ━━━
1. [共通パターンの問題と解決策]
2. [アーキテクチャ変更が必要な項目]
3. [テスト追加が必要な項目]
```

## 設定

| 項目 | 値 |
|------|-----|
| バッチサイズ | 3ファイル |
| 最大イテレーション | 5回 |
| 合格閾値 | 80点 |
| スコア配分 | Security 25, Correctness 25, Performance 20, Maintainability 20, Testing 10 |

## 注意事項

- 各バッチの修正中、同じスコアが2回連続したらそのバッチはスキップ（改善不可と判断）
- CRITICALとHIGHの問題を優先的に修正
- アーキテクチャ変更が必要な問題は失敗リストに理由を記載
