---
layout: post
title: "最近のGitHub活動まとめ: dotfiles刷新、Astro移行、Docker実験"
author: u1e2k
categories: [ dotfiles, web-dev, linux ]
tags: [nix, hyprland, astro, bun, docker, home-manager]
image: assets/images/11.jpg
description: "2026年9月現在の個人プロジェクト更新状況。Nix Flakes + Home Managerでdotfilesを再構築、個人サイトをJekyllからAstro+Bunへ移行、DockerでHyprland動作検証など。"
featured: true
hidden: false
---

久しぶりにブログを更新。ここ半年〜1年でGitHubでやってきたことをまとめておく。

## 🔧 dotfiles: Nix Flakes + Home Manager で完全再構築

**リポジトリ**: [u1e2k/dotfiles](https://github.com/u1e2k/dotfiles)  
**最新更新**: 2026-09-03（20時間前）

### やったこと

- **Nix Flakes + Home Manager** で宣言的環境管理へ移行
- **Hyprland / Kitty / Fish / Alacritty / GTK / fcitx5 / btop / micro / Qt / フォント** などの設定を一括バックアップ・管理
- **インタラクティブセットアップスクリプト** (`setup-interactive.sh`) を追加し、カテゴリ別・項目別で必要なものだけリンク可能に
- `check.sh` でシンボリックリンク状態を検証できるように

### 構成のハイライト

```
dotfiles/
├── flake.nix          # Nix Flakes 定義
├── home.nix           # Home Manager 設定（パッケージ・プログラム定義）
├── setup-interactive.sh  # 対話型インストール（推奨）
├── install.sh         # 全自動リンク（バックアップ付き）
├── check.sh           # リンク状態検証
└── .config/           # 各アプリ設定
    ├── hypr/          # Hyprland ウィンドウマネージャ
    ├── kitty/         # ターミナル
    ├── waybar/        # ステータスバー
    ├── fcitx5/        # 日本語入力
    └── ...
```

### クイックスタート

```bash
# Nix インストール（初回のみ）
sh <(curl -L https://nixos.org/nix/install) --daemon

# リポジトリクローン
git clone https://github.com/u1e2k/dotfiles ~/dotfiles
cd ~/dotfiles

# 対話型でセットアップ（推奨）
./setup-interactive.sh

# または全自動
./setup-interactive.sh --all
```

Nix経由で入る主なパッケージ: `hyprland`, `waybar`, `wofi`, `kitty`, `neovim`, `tmux`, `git`, `ripgrep`, `fd`, `fzf`, `btop`, `eza`, `bat` など。

---

## 🌐 u1e2k.github.io: Jekyll → Astro + Bun へ移行

**リポジトリ**: [u1e2k/u1e2k.github.io](https://github.com/u1e2k/u1e2k.github.io)  
**移行日**: 2025-12-03（10ヶ月前）

### 移行の動機

- Jekyll のビルド速度・ホットリロードに不満
- モダンなツールチェーン（Bun + Astro）を試したい
- Content Collections で型安全な Markdown 管理がしたい

### 新構成

- **SSG**: Astro (v5+)
- **ランタイム**: Bun (高速インストール・実行)
- **スタイリング**: Sass/SCSS (従来資産を活用)
- **デプロイ**: GitHub Actions → GitHub Pages

### 機能

- ⚡️ 高速ビルド・ホットリロード (`bun run dev`)
- 📝 Markdownブログ (Content Collections でフロントマター型チェック)
- 🎨 レスポンシブ + ダークモード
- ✨ アニメーション背景

```bash
# ローカル開発
bun install
bun run dev      # http://localhost:4321
bun run build    # 本番ビルド
```

ブログ記事は `src/content/posts/` に Markdown で追加するだけ。

---

## 🐳 mydocker: Docker で Hyprland 動作検証

**リポジトリ**: [u1e2k/mydocker](https://github.com/u1e2k/mydocker)  
**期間**: 2025-06 ごろ（実験的・現在はアーカイブ気味）

### 目的

- コンテナ上で Wayland/Hyprland が動くか検証
- CI での GUI テスト自動化の足がかりに

### 構成

- `Dockerfile`: Archベース + Hyprland + Wayland 周り
- `run.sh` / `run_hyprland_test.sh`: 起動スクリプト
- GitHub Actions でビルドテストのみ実行（画面が必要なテストはスキップ）

### 学び

- Docker で Wayland コンポジタを動かすには `--device=/dev/dri` 等の特権が必要
- ヘッドレス環境では `wayland` ソケットの扱いが面倒
- 実用的な GUI テストには `xvfb` や `weston-headless` 併用が現実的

---

## 📋 まとめ：今のスタック

| 用途 | ツール |
|------|--------|
| **OS/環境管理** | Nix Flakes + Home Manager (CachyOS/Arch上) |
| **ウィンドウマネージャ** | Hyprland |
| **ターミナル** | Kitty |
| **シェル** | Fish (interactive) / Bash (scripts) |
| **エディタ** | Neovim |
| **バージョン管理** | Git + GitHub |
| **個人サイト** | Astro + Bun + GitHub Pages |
| **ブログ (このサイト)** | Jekyll + GitHub Pages (別リポジトリ `u1e2k/blog`) |

---

## 🔗 参考リンク

- [dotfiles README](https://github.com/u1e2k/dotfiles#readme) - セットアップ手順・パッケージ一覧・スクリプト詳細
- [u1e2k.github.io README](https://github.com/u1e2k/u1e2k.github.io#readme) - Astro移行ガイド・開発手順
- [mydocker](https://github.com/u1e2k/mydocker) - Docker実験の記録

---

次は dotfiles 周りで「Nix Flakes 入門」や「Hyprland 設定のポイント」あたりを個別に記事化しようかな。  
このブログ自体（`u1e2k/blog`）も Astro へ移行するか迷い中…。