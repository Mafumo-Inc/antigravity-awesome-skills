---
name: backend-dev-guidelines
description: Node.js/Express/TypeScript マイクロサービスのバックエンド開発ガイドライン。ルート、コントローラー、サービス、リポジトリ、ミドルウェアの実装、Prisma、Sentry、Zodの使用方法、アーキテクチャ設計をカバーします。
---

# Backend Development Guidelines

Node.js/Express/TypeScript マイクロサービス（API, Auth, Notificationsなど）における一貫した開発パターンとベストプラクティスを提供します。

## 主な機能（自動アクティベーション）
- ルート、コントローラー、サービス、リポジトリの作成・修正
- ミドルウェア（認証、バリデーション、エラー処理）の実装
- Prismaによるデータベース操作
- Sentryによるエラートラッキング設定
- Zodによる入力バリデーション

## クイックスタート: 新機能実装チェックリスト
- [ ] **Route**: コントローラーへの委譲のみを行う（ロジックを書かない）。
- [ ] **Controller**: `BaseController` を継承する。
- [ ] **Service**: ビジネスロジックを実装し、DIを利用する。
- [ ] **Repository**: 複雑なデータアクセスを行う場合に使用（Serviceから呼ぶ）。
- [ ] **Validation**: Zodスキーマですべての入力を検証する。
- [ ] **Sentry**: エラーはすべてキャプチャする。
- [ ] **Config**: `process.env` を直接使わず、`unifiedConfig` を使用する。

## アーキテクチャ原則
```
HTTP Request → Routes(ルーティング) → Controllers(処理) → Services(ロジック) → Repositories(DB) → Database
```
**原則**: 各レイヤーは単一の責任を持つこと。

## 禁止パターン (Anti-Patterns)
❌ ルート内にビジネスロジックを書く
❌ `process.env` を直接コード内で使う
❌ 入力バリデーションを省略する
❌ `console.log` でエラー出力する (Sentryを使う)
