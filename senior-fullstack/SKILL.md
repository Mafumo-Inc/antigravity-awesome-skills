---
name: senior-fullstack
description: React, Next.js, Node.js, GraphQL, PostgreSQL を用いたフルスタック開発のための包括的なガイドとツールキット。新規プロジェクト構築、コード品質分析、設計パターン実装時に使用。
---

# Senior Fullstack

モダンなフルスタック開発のためのベストプラクティスと自動化ツールを提供します。

## コア機能（スクリプト）

以下のスクリプトを使用して、プロジェクトのスカフォールディングや品質分析を行います。

```bash
# 1. フルスタック・スカフォールディング（新規構築）
python scripts/fullstack_scaffolder.py [options]

# 2. プロジェクト分析・最適化建議
python scripts/project_scaffolder.py <target-path> [--verbose]

# 3. コード品質分析
python scripts/code_quality_analyzer.py [options]
```

## 参照ドキュメント
詳細なガイドは `references/` ディレクトリにあります（英語）。
- `tech_stack_guide.md`: 技術スタックとパターン
- `architecture_patterns.md`: アーキテクチャ設計
- `development_workflows.md`: 開発ワークフロー

## ベストプラクティス・サマリ
- **品質**: 確立されたパターンに従い、テストを書く。
- **パフォーマンス**: 推測せず計測する。適切なキャッシュ戦略。
- **セキュリティ**: 入力検証、認証、依存関係の更新。
- **保守性**: 明快な命名、シンプルさの維持。
