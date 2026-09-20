---
title: "【自宅鯖】OMRON BY50S UPS監視システム構築・Grafana/Discord連携とProxmox連動"
date: 2026-09-19T18:30:00Z
tags:
  - homelab
  - ups
  - raspberry-pi
  - prometheus
  - grafana
  - nut
  - proxmox
  - alerting
categories:
  - Infrastructure
  - Homelab
slug: "omron-by50s-ups-grafana-discord"
cover:
  image: "assets/images/thumb_ups_monitoring.jpg"
  alt: "OMRON BY50S UPS Monitoring with Grafana, Prometheus and Discord"
description: "OMRON BY50S 小型UPSをRaspberry Pi Zero・NUT経由でPrometheus / Grafanaと統合し、NOC風ダッシュボードの構築やDiscord停電アラート、Proxmox VE安全シャットダウン連動までを構築した記録です。"
---

自宅環境の電源保護・可視化のため、小型UPS（OMRON BY50S）を Raspberry Pi Zero に USB 接続し、**NUT (Network UPS Tools)** 経由で **Prometheus / Grafana / Discord アラート** と統合しました。

さらに、停電発生時には Proxmox VE（PVE Host）側へシャットダウンシグナルを送り、VM/LXC を安全に退避させる自動連動システムを構築しています。

---

## 1. システム構成・アーキテクチャ概要

```text
[ 商用電源 (AC 100V) ]
         │
[ OMRON BY50S (UPS) ]
         │ (USB接続)
[ Raspberry Pi Zero (ARMv6) ]
    ├── NUT Driver (blazer_usb)
    ├── upsd (Port: 3493 / NUT Master)
    └── nut_exporter (Port: 9199)
         │ (HTTP Scrape / 15s)
[ Prometheus ]
         │
[ Grafana ]
    ├── NOC風 UPSダッシュボード (電圧波形 / 温度 / 状態タイムライン)
    └── Grafana Alerting ──> [ Discord Webhook (#alert) ]
         │
[ Proxmox VE (PVE Host) ] ── (nut-client / slave) ※安全停止連動
```

---

## 2. NUT Exporter セットアップ (Raspberry Pi Zero)

### 2.1 ハードウェア仕様とドライバの制約

* **使用ドライバ:** `blazer_usb`
* **取得可能メトリクス:** 入力電圧、出力電圧、バッテリー電圧、バッテリー残量、商用周波数、本体内部温度、負荷率、ステータス（OL/OB/BYPASS等）
* **非対応項目:** `battery.runtime`（残り稼働秒数の計算値）はハードウェア仕様上出力不可。

### 2.2 温度・出力電圧を有効化する Exporter 設定

デフォルトの `nut_exporter` は主要項目のみをエクスポートするため、引数 `--nut.vars_enable` に追加変数を明示指定します。

`/etc/systemd/system/nut-exporter.service`:

```ini
[Unit]
Description=Network UPS Tools Prometheus Exporter
After=network.target nut-server.service

[Service]
Type=simple
ExecStart=/usr/local/bin/nut_exporter \
  --nut.vars_enable="battery.charge,battery.voltage,battery.voltage.nominal,input.voltage,input.voltage.nominal,output.voltage,input.frequency,ups.load,ups.status,ups.temperature"
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

反映コマンド:

```bash
sudo systemctl daemon-reload
sudo systemctl restart nut-exporter
```

メトリクス疎通確認:

```bash
curl -s "http://localhost:9199/ups_metrics?target=by50s" | grep -E "temperature|output_voltage"
# 出力例:
# network_ups_tools_output_voltage 103.9
# network_ups_tools_ups_temperature 47.3
```

---

## 3. Prometheus スクレイプ設定

`prometheus.yml`（配置場所: `/etc/prometheus/prometheus.yml`）に以下を定義します:

```yaml
scrape_configs:
  - job_name: "nut_ups_rpi_zero"
    static_configs:
      - targets: ["<RPI_IP>:9199"] # Raspberry Pi Zero の IP
    metrics_path: "/ups_metrics"
    params:
      target: ["by50s"]
    scrape_interval: 15s
```

---

## 4. Grafana NOC風ダッシュボード設計

### 4.1 パネル構成

1. **上段 (HUD / Statパネル):**
   * **Power Source:** `network_ups_tools_ups_status{flag="OL"}` (1: ONLINE[緑], 0: ON BATTERY[赤])
   * **Battery Charge:** `network_ups_tools_battery_charge` (Gauge: 0-100%)
   * **Input Voltage:** `network_ups_tools_input_voltage` (Stat / Sparkline)
   * **Load %:** `network_ups_tools_ups_load` (Stat / Sparkline)
   * **Internal Temp:** `network_ups_tools_ups_temperature` (Stat / 閾値: 45℃黄, 55℃赤)

2. **中段 (Time Series / 履歴波形):**
   * **Voltage Waveforms:** Input (`network_ups_tools_input_voltage`) vs Output (`network_ups_tools_output_voltage`) のグラデーション2系列表示
   * **Load History (%):** 負荷率の推移グラフ

3. **下段 (Health & Timeline):**
   * **Power State Timeline:** `network_ups_tools_ups_status{flag=~"OL|OB|BYPASS"} == 1`（State Timeline パネルで停電・復電履歴をバー表示）
   * **Battery Voltage Trend:** `network_ups_tools_battery_voltage`（フロート充電推移: 13.5V〜13.6V）

---

## 5. Grafana Alerting & Discord 連携

### 5.1 Discord Contact Point 設定

* **Type:** Discord
* **Webhook URL:** Discord の「チャンネル設定」→「連携サービス」→「ウェブフック」から取得した URL

### 5.2 停電検知ルール仕様

* **ルール名:** `[CRITICAL] UPS On Battery (停電検知)`
* **フォルダ:** `UPS`
* **評価グループ:** `ups-check` (Interval: `30s`)
* **クエリ (PromQL / Code Mode):**
  ```promql
  network_ups_tools_ups_status{flag="OB"}
  ```
* **判定条件:** `IS ABOVE 0`（バッテリー駆動フラグが 1 になったら発報）
* **保留期間 (Pending):** `30s`（瞬間的な電圧ドロップによる誤爆防止）
* **発生を継続:** `0s`（復電時に即時 Resolved 通知を送信）
* **通知メッセージ:**
  * **Summary:** `商用電源断: UPSがバッテリー稼働に切り替わりました`
  * **Description:** `OMRON BY50Sが商用電源を喪失し、バッテリー駆動（OB）を開始しました。`

---

## 6. Proxmox VE 連動設定（安全シャットダウン）

Pi Zero（Master）から Proxmox VE（Slave / netclient）へ停電シグナルを送り、VM/LXC 退避後に PVE ホストを安全にシャットダウンさせる構成です。

### Step 1: Pi Zero (NUT Server) の外部受付許可

`/etc/nut/upsd.conf`:

```ini
LISTEN 127.0.0.1 3493
LISTEN 0.0.0.0 3493
```

`/etc/nut/upsd.users`:

```ini
[monuser]
    password = <SECURE_PASSWORD>
    upsmon slave
```

### Step 2: Proxmox VE (NUT Client) 設定

```bash
apt update && apt install -y nut-client
```

`/etc/nut/nut.conf`:

```ini
MODE=netclient
```

`/etc/nut/upsmon.conf`:

```ini
MONITOR by50s@<RPI_IP>:3493 1 monuser <SECURE_PASSWORD> slave
SHUTDOWNCMD "/sbin/shutdown -h +0"
POWERDOWNFLAG /etc/killpower
```

有効化:

```bash
systemctl enable --now nut-client
upsc by50s@<RPI_IP>:3493
```

---

## 7. トラブルシューティングの記録

| 現象 | 原因 | 解決策 |
| :--- | :--- | :--- |
| **Grafanaで「An error occurred within the plugin」** | Prometheus の IP 変更に伴うデータソース参照切れ | Data sources 設定で新 IP を入力し `Save & test` で再疎通確認。 |
| **ダッシュボードで「Battery Run Time Left」がデータなし** | BY50S (`blazer_usb`) はハードウェア仕様上 `battery.runtime` を返さない | 当該パネルを削除し、レイアウトを最適化。 |
| **ダッシュボードで「Internal Temp」「Output Voltage」が出ない** | `nut_exporter` の初期収集変数に含まれていなかった | `nut-exporter.service` に `--nut.vars_enable` 引数を追加して再起動。 |
| **アラートルール保存時に「コンタクトポイントが必要です」エラー** | Grafana のバージョン仕様による必須検証エラー | 先に作成した `Discord-UPS` を明示的に指定して保存。 |
