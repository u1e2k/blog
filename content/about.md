---
title: "About"
url: "/about/"
summary: "u1e2k のプロフィールとこのブログについて"
description: "フリーランス インフラエンジニア / メディアオペレーター / ツール・ゲームクリエイター"
hideMeta: true
---

## 👤 Profile

フリーランスのインフラエンジニアおよびダンスイベントのメディアオペレーターとして活動しています。  
「**世の中をもっと便利に、面白くする**」をモットーに、自宅サーバー・ネットワーク構築から、日々の課題を自動化する自作ツール開発、Godot Engine によるゲーム制作まで幅広く取り組んでいます。

---

## 🤖 AI & エージェント駆動ワークフロー

日常の開発からシステム運用・ナレッジ管理まで、AI エージェントを日常の基盤として組み込んでいます。

| 領域 | 活用エージェント | 役割と実践内容 |
| :--- | :--- | :--- |
| **プロダクト開発** | **Gemini ✕ Antigravity** | Gemini で構想・アーキテクチャを壁打ちしてプロンプト化し、Antigravity が実装・テストを並走 |
| **ナレッジ管理** | **Hermes Agent ✕ Obsidian** | 日常の思考や技術検証メモを Obsidian に集約し、エージェントがノート整理や自動更新を自律代行 |
| **開発環境保守** | **Hermes Agent ✕ CachyOS** | Hyper-V 上の CachyOS 環境にて、Neovim や WezTerm の設定変更・チューニングをエージェントが自律実行 |

---

## 🛠️ システム構成 & 技術スタック

### 🖥️ メインデスクトップ環境 (Client)

| コンポーネント | 採用技術・ツール | 概要・用途 |
| :--- | :--- | :--- |
| **母艦 OS** | **Windows 11 Pro** | 日常のメイン作業基盤・GUI・各種エージェント統合 |
| **仮想化 / Linux** | **Hyper-V ✕ CachyOS** | メイン Linux 開発環境を常設運用 |
| **ナレッジ基盤** | **Obsidian** | 思考メモ・技術ドキュメントの主たる保存庫 |
| **エディタ** | **Neovim** | ターミナル作業・コーディング（エージェント保守） |
| **ターミナル / シェル** | **WezTerm** / Kitty / Fish / Bash | 高速ターミナル ✕ インタラクティブシェル |
| **環境再現性** | **Nix Flakes + Home Manager** | dotfiles の宣言的管理 |

### 🖧 自宅サーバー & クラスタ (Homelab)

| レイヤー | 採用技術・ツール | 概要・用途 |
| :--- | :--- | :--- |
| **仮想化基盤** | **Proxmox VE 9.2** | 小型サーバー複数ノードによる高可用クラスタ |
| **可観測性** | **Prometheus ✕ Grafana** | メトリクス収集と NOC 風リアルタイム監視ダッシュボード |
| **ネットワーク** | **NPM ✕ Tailscale ✕ SDN** | リバースプロキシ・メッシュ VPN・ゾーン分離 |
| **コンテナ・VM** | **LXC ✕ QEMU VM** | 各種軽量サービス、ゲーム鯖 (Project Zomboid)、Windows Server 2025 |
| **電源保護** | **OMRON BY50S ✕ Pi Zero** | NUT Master 連携・停電検知発報 & Proxmox 安全停止連動 |

---

## 🌟 主なプロジェクト・制作物

| プロジェクト | 概要 | 主な技術 |
| :--- | :--- | :--- |
| 💎 **[u1e2k/mumbler](https://github.com/u1e2k/mumbler)** | Obsidian 向けタイムライン／マイクロブログ投稿プラグイン | `TypeScript` `Obsidian API` |
| ⚡ **[u1e2k/tsub](https://github.com/u1e2k/tsub)** | ターミナルから 1 秒でデイリーノートへメモ投稿・閲覧できる TUI CLI | `Go` `TUI` `CLI` |
| 🎮 **[u1e2k/squareman](https://github.com/u1e2k/squareman)** | Godot 4 による 2D アクション／パズルゲームプロジェクト | `Godot 4` `GDScript` |
| 🎮 **[u1e2k/poteverse](https://github.com/u1e2k/poteverse)** | Godot Engine を活用したゲーム制作プロトタイプ | `Godot 4` `GDScript` |
| 🎮 **[u1e2k/othellonly](https://github.com/u1e2k/othellonly)** | Godot 4 によるリバーシ（オセロ）ゲームプロジェクト | `Godot 4` `GDScript` |
| ❄️ **[u1e2k/dotfiles](https://github.com/u1e2k/dotfiles)** | Nix Flakes + Home Manager で管理する再現性の高い個人開発環境 | `Nix` `Shell` `Linux` |
| 📝 **[u1e2k/blog](https://github.com/u1e2k/blog)** | このブログのソースコード（Hugo + PaperMod） | `Hugo` `HTML/CSS` |

---

## 📝 発信メディア & リンク

| メディア | リンク / アカウント | 主な発信内容 |
| :--- | :--- | :--- |
| 🐙 **GitHub** | [@u1e2k](https://github.com/u1e2k) | ソースコード、自作ツール、dotfiles、OSS 活動 |
| 📑 **note** | [note.com/u1e2k](https://note.com/u1e2k) | サーバー構築、Proxmox LXC、ハードウェア分解検証、コラム |
| 🐦 **X (Twitter)** | [@u1e2k](https://x.com/u1e2k) | 日常のつぶやき、技術検証の進捗速報 |
| 📊 **LAPRAS** | [LAPRAS Profile](https://lapras.com/public/SGDKQTX) | スキル・活動ポートフォリオ |

---

## 📄 ライセンス

- 記事の文章コンテンツ: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)
- コードスニペット / ソースコード: [MIT License](https://opensource.org/licenses/MIT)

自由に引用・改変していただけます（クレジット表記をお願いします）。

---

<small>このサイトは [Hugo](https://gohugo.io/) と [PaperMod](https://github.com/adityatelange/hugo-PaperMod) で構築され、[GitHub Pages](https://pages.github.com/) でホストされています。ソースコードは [GitHub](https://github.com/u1e2k/blog) で公開中です。</small>