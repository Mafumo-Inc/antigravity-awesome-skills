---
name: ui-ux-pro-max
description: 50以上のスタイル、パレット、フォントペアリング、UXガイドラインを網羅したUI/UXデザインインテリジェンス。React, Tailwind, Shadcn/ui等のスタックに対応し、設計から実装、レビューまでを支援します。
---

# UI/UX Pro Max

Web/モバイルアプリのための包括的なデザインガイドライン兼ツールキットです。
スタイル、カラーパレット、アクセシビリティ、UXルールを検索・適用できます。

## 優先順位（Priority）

1.  **Accessibility (最優先)**: コントラスト比、フォーカス状態、Altテキスト。
2.  **Touch & Interaction**: タップ領域サイズ (44px+)、ホバーとタップの区別。
3.  **Performance**: 画像最適化、CLS防止。
4.  **Layout**: レスポンシブ対応、ビューポート設定。

## 使い方の流れ

UI/UXの実装やレビューを行う際、以下のコマンドでベストプラクティスを検索します。

### 1. デザインシステムの生成 (必須)
プロジェクトの方向性を決めるための包括的な定義を取得します。

```bash
python3 .claude/skills/ui-ux-pro-max/scripts/search.py "<product_type> <industry> <keywords>" --design-system
# 例: python3 ... "beauty spa elegant" --design-system
```

### 2. 詳細情報の検索
特定のドメインについて深く知りたい場合に使用します。

```bash
python3 .claude/skills/ui-ux-pro-max/scripts/search.py "<keyword>" --domain <domain>
# ドメイン例: ux, style, typography, color, chart, landing
```

### 3. 実装スタック別のガイド
特定の技術スタックに向けた実装ガイドを取得します。

```bash
python3 .claude/skills/ui-ux-pro-max/scripts/search.py "<keyword>" --stack html-tailwind
# スタック例: react, nextjs, vue, shadcn
```

## プロ品質のためのチェックリスト (Pre-Delivery)

- [ ] **No Emoji Icons**: 絵文字をアイコンとして使わない（SVGを使う）。
- [ ] **Cursor Pointer**: クリック可能な要素には必ず `cursor-pointer` をつける。
- [ ] **Hover Feedback**: ホバー時に明確な変化（色、影など）をつける。
- [ ] **Glass/Light Mode**: ガラス表現や薄い色はライトモードでの視認性を確認する。
- [ ] **Visual Hierarchy**: 重要な要素が視覚的に目立っているか確認する。
