---
title: "About"
url: "/about/"
summary: "u1e2k のプロフィールとこのブログについて"
description: "フリーランス インフラエンジニア / メディアオペレーター / ツール・ゲームクリエイター"
hideMeta: true
---

## 👤 About Me

フリーランスのインフラエンジニアおよびダンスイベントのメディアオペレーターとして活動しています。  
「**世の中をもっと便利に、面白くする**」をモットーに、自宅小型サーバー・ネットワークの構築運用から、日々の課題を自動化・効率化する自作ツール開発、Godot Engine によるゲーム制作まで幅広く取り組んでいます。

### AI & エージェント駆動のライフスタイル
日常のメイン母艦環境は **Windows 11 Pro** を基盤とし、その上で AI エージェントや Hyper-V 仮想環境を徹底的に組み込んでいます：
- **メイン作業基盤**: **Windows 11 Pro** をホストとし、GUI / ターミナル / エージェント環境をシームレスに統合。
- **プロダクト開発**: **Gemini** で構想・アーキテクチャの壁打ちを行い、プロンプトを練り上げてから **Antigravity**（AI Coding Assistant）に実装してもらう協調開発フローを実践。
- **日常運用・環境保守**: **Hermes Agent** を活用して Obsidian のノート更新を任せたり、Hyper-V 仮想マシン上の **CachyOS** にて **Neovim** や **WezTerm** の設定変更・チューニングをエージェントに自律実行させるなど、日常のツールチェーン全体をエージェントとともに運用。

---

## 🛠️ 現在の主な技術スタック

### 🖥️ メインホスト & デスクトップ環境 (Windows 11 Pro)
- **母艦 OS**: **Windows 11 Pro**（日常のメイン作業基盤）
- **クライアント仮想化**: **Hyper-V** 上で **CachyOS** をゲスト運用し、高速な Linux 開発環境を常設
- **エディタ & ターミナル**: **Neovim**、**WezTerm**、Kitty、Fish (interactive) / Bash (scripts)
- **自律型エージェント保守**: **Hermes Agent** を活用し、Obsidian のノート自動更新や CachyOS 内の Neovim / WezTerm 設定の自律変更・チューニングを委任
- **環境の宣言的管理**: Nix Flakes + Home Manager、dotfiles

### 🖧 自宅サーバー & クラスタインフラ (Proxmox VE)
- **クラスタ基盤**: **Proxmox VE 9.2** 複数ノードクラスタ（HPE ProLiant Micro TM200 等）
- **可観測性 & モニタリング**: **Prometheus** ✕ **Grafana** によるクラスタメトリクス収集・ダッシュボード可視化
- **ネットワーク & プロキシ**: **Nginx Proxy Manager**、**Tailscale**（セキュアなメッシュ VPN 連携）、Proxmox SDN（ゾーン分離・仮想ネットワーク）
- **LXC コンテナ運用**: Prometheus、Grafana、NPM、ゲーム専用サーバー（Project Zomboid など）
- **仮想マシン (QEMU VM)**: Windows Server 2025 評価・検証環境など
- **電源保護 & UPS 監視**: OMRON BY50S ✕ Raspberry Pi Zero (NUT) ✕ Prometheus / Grafana / Discord 停電発報 & Proxmox 安全停止連動

### 🤖 開発スタイル & アプリケーション制作
- **AI ペアプログラミング**: **Gemini** (構想・アーキテクチャ壁打ち・プロンプト生成) ✕ **Antigravity** (実装・テスト・ペアプロ)
- **ゲーム制作**: **Godot Engine 4** (GDScript) による 2D ゲーム開発
- **開発言語**: Go、TypeScript、Python、Shell
- **このブログ**: Hugo (PaperMod) + GitHub Pages

---

## 🌟 主なプロジェクト・制作物

| プロジェクト | 概要 | 主な技術 |
| :--- | :--- | :--- |
| 💎 **[u1e2k/mumbler](https://github.com/u1e2k/mumbler)** | Obsidian 向けタイムライン／マイクロブログ投稿プラグイン | `TypeScript` `Obsidian API` |
| ⚡ **[u1e2k/tsub](https://github.com/u1e2k/tsub)** | ターミナルから 1 秒で Obsidian デイリーノートへメモ投稿・閲覧できる TUI CLI | `Go` `TUI` `CLI` |
| 🎮 **[u1e2k/squareman](https://github.com/u1e2k/squareman)** | Godot 4 による 2D アクション／パズルゲームプロジェクト | `Godot 4` `GDScript` |
| 🎮 **[u1e2k/poteverse](https://github.com/u1e2k/poteverse)** | Godot Engine を活用したゲーム制作プロトタイプ | `Godot 4` `GDScript` |
| 🎮 **[u1e2k/othellonly](https://github.com/u1e2k/othellonly)** | Godot 4 によるリバーシ（オセロ）ゲームプロジェクト | `Godot 4` `GDScript` |
| ❄️ **[u1e2k/dotfiles](https://github.com/u1e2k/dotfiles)** | Nix Flakes + Home Manager で管理する再現性の高い個人開発環境 | `Nix` `Shell` `Linux` |
| 📝 **[u1e2k/blog](https://github.com/u1e2k/blog)** | このブログのソースコード（Hugo + PaperMod） | `Hugo` `HTML/CSS` |

---

## 📝 主な発信メディア & リンク

- 🐙 **GitHub**: [@u1e2k](https://github.com/u1e2k) — ソースコード、自作ツール、dotfiles
- 📑 **note**: [note.com/u1e2k](https://note.com/u1e2k) — サーバー構築・Proxmox LXC・ハードウェア分解・コラムなど
- 🐦 **X (Twitter)**: [@u1e2k](https://x.com/u1e2k) — 日常のつぶやき・開発進捗
- 📊 **LAPRAS**: [LAPRAS Profile](https://lapras.com/public/SGDKQTX) — 技術力・活動ポートフォリオ

---

## 📄 ライセンス

- 記事の文章コンテンツ: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/)
- コードスニペット / ソースコード: [MIT License](https://opensource.org/licenses/MIT)

自由に引用・改変していただけます（クレジット表記をお願いします）。

---

<small>このサイトは [Hugo](https://gohugo.io/) と [PaperMod](https://github.com/adityatelange/hugo-PaperMod) で構築され、[GitHub Pages](https://pages.github.com/) でホストされています。ソースコードは [GitHub](https://github.com/u1e2k/blog) で公開中です。</small>