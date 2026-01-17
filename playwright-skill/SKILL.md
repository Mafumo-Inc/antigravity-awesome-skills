---
name: playwright-skill
description: ブラウザ自動化（テスト、スクリーンショット、フォーム入力など）を実行します。ローカルサーバーを自動検出し、/tmp にスクリプトを生成して実行します。
---

# Playwright Browser Automation

汎用のブラウザ自動化スキルです。要件に応じてPlaywrightコードを生成し実行します。

## 主な機能
- **自動検出**: 起動中のローカル開発サーバー（localhost:3000など）を自動で見つけます。
- **安全な実行**: テストスクリプトは `/tmp` に生成され、プロジェクトを汚しません。
- **可視化**: デフォルトでブラウザを表示 (`headless: false`) して動作を確認できます。

## 使い方

ユーザーの指示（例：「ログイン画面のテストをして」「スマホ表示のスクショを撮って」）に応じて、自動的に以下のステップを実行します。

1.  **サーバー検出**: `detectDevServers()` を実行。
2.  **スクリプト生成**: `/tmp/playwright-test-*.js` にコードを作成。
3.  **実行**: `node run.js /tmp/...` で実行。

## コマンド例（手動実行が必要な場合）

**サーバー検出（最初に行う）**:
```bash
cd $SKILL_DIR && node -e "require('./lib/helpers').detectDevServers().then(s => console.log(JSON.stringify(s)))"
```

**スクリプト実行**:
```bash
cd $SKILL_DIR && node run.js /tmp/playwright-test-target.js
```
