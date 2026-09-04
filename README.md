# u1e2k blog

Jekyll + GitHub Pages で構築された個人ブログ。自作ミニマルテーマ（Bootstrap/jQuery/Font Awesome 非依存、ダークモード対応）。

## 🚀 クイックスタート

### 記事を書く

```bash
# _posts/ にマークダウンファイルを作成
# ファイル名形式: YYYY-MM-DD-slug.md
touch _posts/2026-09-04-new-article.md
```

フロントマター例：

```yaml
---
layout: post
title: "記事タイトル"
author: u1e2k
categories: [tools, web-dev]
tags: [nix, hyprland, astro]
image: assets/images/cover.jpg
description: "記事の概要（SEO・OGP・カード表示用）"
featured: false      # true にするとトップの「注目記事」に表示
hidden: false        # true にすると一覧から非表示
toc: true            # 目次を表示
rating: 4.5          # レビュー記事の場合（Schema.org Review）
---

記事本文をマークダウンで書く...
```

### ローカルで確認

```bash
# 依存インストール（初回のみ）
bundle install

# 開発サーバー起動
bundle exec jekyll serve --livereload

# http://localhost:4000/blog/ で確認
```

### デプロイ

```bash
git add _posts/2026-09-04-new-article.md
git commit -m "Add new article"
git push origin main
```

GitHub Actions (`jekyll-gh-pages.yml`) が自動でビルド・デプロイします。

## 🎨 テーマの特徴

- **依存ゼロ**: Bootstrap / jQuery / Font Awesome / Google Fonts 不使用
- **システムフォント**: OS 標準フォントスタックで高速・軽量
- **ダークモード**: OS 設定連動 + 手動切替 + localStorage 保存
- **レスポンシブ**: モバイルファースト、記事幅 720px で読みやすさ重視
- **アクセシビリティ**: スキップリンク、ARIA、セマンティック HTML、フォーカス表示
- **シンタックスハイライト**: GitHub ライト/ダークテーマ準拠（Rouge）
- **NEW バッジ**: 投稿から 14 日以内の記事に自動表示

## 📁 ディレクトリ構成

```
.
├── _config.yml              # Jekyll 設定
├── _layouts/
│   ├── default.html         # ベースレイアウト
│   ├── post.html            # 記事レイアウト
│   ├── categories.html      # カテゴリ一覧
│   └── tags.html            # タグ一覧
├── _includes/
│   └── search-lunr.html     # 検索フォーム（DuckDuckGo site:検索）
├── _sass/
│   ├── _variables.scss      # デザイントークン
│   ├── _reset.scss          # リセット + ベース
│   ├── _header.scss         # ヘッダー/ナビ/テーマ切替
│   ├── _layout.scss         # 汎用レイアウト/カード/NEWバッジ
│   ├── _post.scss           # 記事専用スタイル
│   ├── _footer.scss         # フッター
│   └── _syntax.scss         # シンタックスハイライト
├── _posts/                  # 記事（マークダウン）
├── _pages/
│   ├── about.md             # About ページ
│   ├── categories.md        # カテゴリ一覧ページ
│   └── tags.md              # タグ一覧ページ
├── assets/
│   ├── css/main.scss        # Sass エントリーポイント
│   ├── images/              # 画像
│   └── js/                  # 最小限のJS（テーマ切替のみ）
└── .github/workflows/
    └── jekyll-gh-pages.yml  # GitHub Pages デプロイ
```

## ⚙️ カスタマイズ

### 色・スペーシング等の変更

`_sass/_variables.scss` の CSS カスタムプロパティを編集：

```scss
:root {
  --color-accent: #0066cc;        // アクセントカラー
  --container-max: 720px;         // 記事最大幅
  --font-size-base: 1rem;         // 基準フォントサイズ
  --space-md: 1rem;               // 基準スペース
  // ...
}
```

### NEW バッジの閾値変更

`index.html`、`_layouts/categories.html`、`_layouts/tags.html` の：

```liquid
{% if post_age < 14 %}  // ここを変更（日数）
```

### 検索の置き換え

`_includes/search-lunr.html` を Algolia DocSearch / Pagefind 等に差し替え可能。

## 📝 ライセンス

記事: CC BY 4.0 / コード: MIT License  
テーマ・設定ファイル: 自由に利用・改変可能

---

最終更新: 2026-09-04