---
layout: post
title: "Antigravity が Windows のターミナルサンドボックス初期化でクラッシュする問題 (0xc0000005) の調査メモ"
author: u1e2k
categories: [ tools, windows, antigravity ]
tags: [antigravity, windows, sandbox, bug, golang, powershell]
image: assets/images/12.jpg
description: "Windows 版 Antigravity で、エージェントがターミナルコマンドを実行するたびに言語サーバーがアクセス違反 (0xc0000005) でクラッシュする問題の再現手順・スタックトレース・原因分析・回避策のまとめ。"
featured: false
hidden: false
date: 2026-11-01T03:40:00+09:00
slug: "antigravity-windows-sandbox-crash"
cover:
  image: "assets/images/12.jpg"
---

Windows 上の Antigravity で、エージェントにターミナル (PowerShell) コマンドを実行させるたびに **言語サーバーがアクセス違反 (`Exception 0xc0000005`) でクラッシュする** 問題に遭遇しました。調査した内容をバグレポートとしてまとめます。

## 概要

ターミナルサンドボックスの初期化処理 (`exebox.New` → `initPSEC` → `psecHidden` → `hasAllAppPackagesAccess` → `aclHasSID` → `getAclInformation`) の中で、**NULL の ACL ポインタ (`0x0`) が Windows API に渡される** ことでクラッシュします。

Electron アプリには `Language server crashed: exited with code 2` と記録され、言語サーバーは自動で再起動しますが、エージェントのコマンドは実行されません。

## 環境

| 項目 | 値 |
|---|---|
| OS | Windows 11 Pro 10.0.26300 (build 26300), x64 |
| Antigravity (2.0 app) | 2.28.2 |
| Antigravity IDE | 2.6.0 |
| Language server | 2.28.2 |
| 既定のシェル | PowerShell |

## 再現手順

1. ターミナルサンドボックスを有効 (Default プリセット) にして、Windows 上で Antigravity (2.0 app または IDE) を起動する。
2. エージェントに任意のターミナルコマンド (例: `Get-Date`) を実行させる。
3. 言語サーバーが即座にクラッシュし、アプリが再起動する。

**再現率:** 100% (2026-11-01 01:54〜03:26 JST の間に 10 回以上クラッシュ)

## 期待される動作と実際の動作

- **期待:** サンドボックス内でコマンドが実行され、出力が返る。
- **実際:** 言語サーバーのプロセスがアクセス違反で終了し、コマンドの出力は得られない。

## 証跡

### Electron の `main.log`

```
[2026-11-01 03:24:03.728] [error] Language server crashed: exited with code 2
[2026-11-01 03:24:03.729] [info]  Attempting restart 1/3 in 2s...
```

### 言語サーバーログ: クラッシュ直前の行

クラッシュしたセッションは、コマンド実行開始時に出力される次の行の直後で必ず終了していました。

```
I1101 03:25:58.356532    2347 trusted_escape.go:172] [trusted-cli-escape] escapable binaries=[]
I1101 03:25:58.356532    2347 trusted_escape.go:174] [trusted-cli-escape] no escapable binaries; escape wiring skipped
```

### クラッシュ時のスタックトレース

`language_server.log` は再起動時に上書きされるため、上書き前に採取したものです。

```
Exception 0xc0000005 0x0 0x0 0x7ffd17f3bec6
PC=0x7ffd17f3bec6
signal arrived during external code execution

runtime.cgocall(0x7ff7368750c0, 0x2f6f2ada358)
	third_party/go/gc/src/runtime/cgocall.go:167
syscall.syscalln(...)
	third_party/go/gc/src/runtime/syscall_windows.go:427
syscall.SyscallN(...)
	third_party/go/gc/src/syscall/dll_windows.go:99
google3/third_party/golang/go_sys/windows/windows.(*Proc).Call(...)
	third_party/golang/go_sys/windows/dll_windows.go:166
google3/third_party/golang/go_sys/windows/windows.(*LazyProc).Call(0x7ff741276dc0, {0x2f6f40b82e0, 0x4, 0x4})
	third_party/golang/go_sys/windows/dll_windows.go:316
google3/devtools/ai/sandbox/exebox.getAclInformation(0x0, 0x0?, 0xc, 0x2)      <-- ACL pointer is NULL
	devtools/ai/sandbox/exebox_winapi.go:72
google3/devtools/ai/sandbox/exebox.aclHasSID(0x0, 0x2f6f17f2960)
	devtools/ai/sandbox/exebox_windows.go:676
google3/devtools/ai/sandbox/exebox.hasAllAppPackagesAccess({0x2f6f5174400, 0x28})
	devtools/ai/sandbox/exebox_windows.go:667
google3/devtools/ai/sandbox/exebox.psecHidden({0x2f6f4020780, 0x1e}, 0x0, 0x2f6f2a079c0)
	devtools/ai/sandbox/exebox_psec_windows.go:240
google3/devtools/ai/sandbox/exebox.(*Box).psecFiles(0x2f6f23be460)
	devtools/ai/sandbox/exebox_psec_windows.go:159
google3/devtools/ai/sandbox/exebox.(*Box).initPSEC(0x2f6f23be460)
	devtools/ai/sandbox/exebox_psec_windows.go:40
google3/devtools/ai/sandbox/exebox.(*Box).init(0x2f6f23be460)
	devtools/ai/sandbox/exebox_windows.go:57
google3/devtools/ai/sandbox/exebox.newBox(0x2f6f2079590)
	devtools/ai/sandbox/exebox.go:309
google3/devtools/ai/sandbox/exebox.New(...)
	devtools/ai/sandbox/exebox.go:234
google3/third_party/jetski/cortex/command/command.(*goRunner).Run.func2()
	third_party/jetski/cortex/command/go_command.go:724
created by google3/third_party/jetski/cortex/command/command.(*goRunner).Run in goroutine 2347
	third_party/jetski/cortex/command/go_command.go:723
```

## 原因の分析

- `aclHasSID` が NULL の ACL (`0x0`) を受け取り、nil チェックなしに `GetAclInformation` へ渡しているため、アクセス違反が発生する。
- `psecHidden` でチェックしているパス (文字列長 `0x1e` = 30)、または `hasAllAppPackagesAccess` のパス (文字列長 `0x28` = 40) のいずれかで、DACL が NULL もしくは取得できていないと考えられる。想定される理由は、NULL DACL、セキュリティ情報の取得失敗、パスが存在しない、のいずれか。
- `%USERPROFILE%` とその直下、ドットフォルダ・`AppData`・`Documents` の 1 階層内の計 386 項目を走査したが、NULL DACL を持つ項目は見つからなかった。問題のパスはこれらの範囲外にある可能性が高い。
- **修正案:** `aclHasSID` / `getAclInformation` で ACL ポインタの NULL チェックを追加する。あわせてセキュリティ記述子の取得エラーを処理し、NULL DACL は参照せずに「フルアクセス」として扱う。

## その他のメモ

- Windows のクラッシュダンプ (`%USERPROFILE%\.gemini\antigravity\crashes\crash_*.log`) は 0 バイトで、Windows のアプリケーションイベントも記録されていない。
- `language_server.log` は再起動のたびに上書きされるため、事前に採取しないとパニック出力が失われる。再起動時に前回のログを保存するようにすれば、診断しやすくなる。
- PowerShell プロファイル (oh-my-posh, Terminal-Icons) を無効化・変更しても、クラッシュには影響しなかった。
- サンドボックスなしで実行したコマンド (Antigravity CLI セッション) は正常に動作する。

## 回避策

ターミナルサンドボックスを無効にすれば回避できるはずですが、**確実な手順は確認できていません**。

- IDE 2.6.0 の設定画面で `sandbox` を検索しても、該当する項目は表示されませんでした。
- 2.0 アプリのドキュメントには、プロジェクト単位の *Sandbox Mode* や Permission Preset といった設定の記載があります。設定名や場所は版によって異なる可能性があるため、2.0 アプリの Settings で `sandbox` を検索して探してください。
- Antigravity CLI のセッション (サンドボックスなし) ではコマンドが正常に動作するため、当面は CLI を使うのも手です。

## 添付ファイル

- 全 goroutine を含む言語サーバーログ: `before_restart_032600_591.log`
- Electron ログ: `%APPDATA%\Antigravity\logs\main.log`

- - - 

んー、でももしかしたらぜんぜん違う原因の可能性がある。
AIは嘘つきなので。
