---
name: git-pushing
description: Conventional Commits に従ったコミットメッセージを生成し、ステージング・コミット・プッシュを一括で行います。「プッシュして」「保存して」などの指示で起動します。
---

# Git Push Workflow

変更をステージングし、AIが生成したConventional Commitメッセージでコミットし、リモートブランチへプッシュします。

## 使い方

ユーザーが以下のような指示を出したときに自動的に使用します：
- 「プッシュして」「GitHubに保存して」
- 「変更を上げて」「コミットしてプッシュ」

## 実行コマンド

**常に以下のスクリプトを使用してください（手動gitコマンド禁止）**:

```bash
bash skills/git-pushing/scripts/smart_commit.sh
```

カスタムメッセージを指定する場合:

```bash
bash skills/git-pushing/scripts/smart_commit.sh "feat: 機能を追加"
```
