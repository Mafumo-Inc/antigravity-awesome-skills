---
name: frontend-dev-guidelines
description: React/TypeScript アプリケーションのフロントエンド開発ガイドライン。Suspense, lazy loading, useSuspenseQuery, 機能別ディレクトリ構成, MUI v7, TanStack Router, パフォーマンス最適化などをカバーします。
---

# Frontend Development Guidelines

モダンな React 開発のためのガイドラインです。Suspense ベースのデータフェッチ、遅延ロード、ディレクトリ構成、パフォーマンス最適化を重視します。

## 主な原則

1.  **Lazy Load Everything**: 重いコンポーネント（DataGrid, チャート等）やルートは `React.lazy` で遅延ロードする。
2.  **Suspense for Loading**: ローディングスピナーによる早期リターン (`if (isLoading) return ...`) は避け、`<SuspenseLoader>` を使う。
3.  **useSuspenseQuery**: 新しいデータ取得には `useSuspenseQuery` を使用する。
4.  **Feature Directory**: 機能ごとのディレクトリ構成 (`features/{name}/api|components|hooks`) を守る。
5.  **Styling**: 100行未満はインライン (`sx` prop)、それ以外は別ファイルにする。
6.  **Aliases**: インポートにはエイリアス (`@/`, `~types`, `~features`) を活用する。

## 新規コンポーネント作成チェックリスト

- [ ] `React.FC<Props>` で型定義する。
- [ ] 必要に応じて `React.lazy` と `Suspense` を適用。
- [ ] `useSuspenseQuery` でデータ取得。
- [ ] ユーザー通知には `useMuiSnackbar` を使用（`react-toastify` は禁止）。
- [ ] 重い計算には `useMemo`、ハンドラには `useCallback` を適用。

## ディレクトリ構成例 (Features)
```
src/features/my-feature/
  ├── api/         # APIサービス層
  ├── components/  # 機能固有コンポーネント
  ├── hooks/       # カスタムフック
  ├── types/       # 型定義
  └── index.ts     # 公開エクスポート
```