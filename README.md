# u1e2k blog

🔗 [https://u1e2k.github.io/blog/](https://u1e2k.github.io/blog/)

Hugo + PaperMod + GitHub Pages で構築された個人技術ブログ。インフラ、Linux、Nix、コンテナ、Web 開発などの備忘録。

---

## 🚀 クイックスタート

### 記事を書く

```bash
# content/posts/ にマークダウンファイルを作成
# 推奨形式: YYYY-MM-DD-slug.md
hugo new posts/2026-09-21-new-article.md
# または直接作成
touch content/posts/2026-09-21-new-article.md
```

フロントマター例：

```yaml
---
title: "記事タイトル"
date: 2026-09-21
slug: "new-article"
author: u1e2k
categories: [tools, linux]
tags: [nix, docker, infrastructure]
cover:
  image: "assets/images/12.jpg"
  alt: "カバー画像"
description: "記事の概要（SEO・OGP・一覧カード表示用）"
draft: false
---

記事本文をマークダウンで書く...
```

### ローカルで確認

```bash
# Hugo 開発サーバー起動（ドラフト記事も含めてプレビュー）
hugo server -D

# ブラウザでアクセス
# http://localhost:1313/blog/
```

### デプロイ

```bash
git add content/posts/2026-09-21-new-article.md
git commit -m "Add new article"
git push origin main
```

GitHub Actions (`.github/workflows/deploy-hugo.yml`) が自動でビルド（`hugo --minify`）し、GitHub Pages にデプロイします。

---

## 🎨 特徴 & カスタマイズ

- **超高速ビルド**: 静的サイトジェネレーター Hugo（Extended）によるミリ秒単位のビルド
- **モダンなマガジンスタイル**:
  - 大画面（1040px 以上）では美しい **3列カードグリッド**
  - タブレット（680px〜）では 2列、モバイルでは 1列の完全レスポンシブ設計
- **トップヒーロー領域**: アバター・自己紹介・SNSリンクを備えたカードデザイン
- **自動 NEW バッジ**: 公開日から指定日数以内（デフォルト: 30日）の記事に赤いグラデーションの `NEW` バッジを自動表示
- **高速な全文検索**: Fuse.js によるクライアントサイド・インクリメンタル検索（`/search/`）
- **年月別アーカイブ**: 過去記事を年・月ごとに一覧表示（`/archives/`）
- **ダークモード**: OS設定自動連動 + ワンクリックでの手動トグル
- **SEO & OGP 最適化**: 自動パンくずリスト、JSON-LD（BlogPosting / BreadcrumbList）完備

---

## 📁 ディレクトリ構成

```
.
├── hugo.yaml                    # Hugo サイト設定（タイトル、パーマリンク、メニュー等）
├── content/
│   ├── posts/                   # 記事マークダウン（YYYY-MM-DD-slug.md）
│   ├── about.md                 # About ページ (/about/)
│   ├── search.md                # 検索ページ (/search/)
│   └── archives.md              # アーカイブページ (/archives/)
├── layouts/                     # テーマの上書き・独自テンプレート
│   ├── list.html                # 記事一覧レイアウト（NEWバッジ判定対応）
│   └── _partials/
│       ├── home_info.html       # トップのヒーロー（アバター・紹介文）
│       └── templates/
│           └── schema_json.html # JSON-LD 構造化データ
├── assets/
│   └── css/extended/
│       └── custom.css           # 独自CSS（3列グリッド、配色、カードスタイル）
├── static/                      # 静的アセット（そのままルート配下に配置）
│   ├── favicon.ico
│   └── assets/images/           # カバー画像・ロゴ等
├── themes/
│   └── PaperMod/                # PaperMod テーマ（Git Submodule）
└── .github/workflows/
    └── deploy-hugo.yml          # GitHub Actions デプロイワークフロー
```

---

## ⚙️ よく使うカスタマイズ

### NEW バッジの期間変更
`hugo.yaml` の `params.newPostDays` を変更：

```yaml
params:
  newPostDays: 14  # 14日以内の投稿にNEWを表示（デフォルト: 30）
```
※ 記事のフロントマターに `isNew: true` を記述すれば、日数に関係なく強制表示も可能です。

### デザインや配色の調整
`assets/css/extended/custom.css` を編集：
- `--accent-color`: テーマのアクセントカラー（デフォルト: `#6366f1`）
- `--main-width`: サイトの最大幅（デフォルト: `1220px`）

---

## 📝 ライセンス

- 記事内容: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)
- ソースコード・設定: [MIT License](https://opensource.org/licenses/MIT)

---

最終更新: 2026-09-20 (Hugo への移行完了)