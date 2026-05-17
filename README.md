# Egern Configuration

<p align="center">
  <img src="https://img.shields.io/badge/Egern-1-blue?style=flat-square" alt="Egern">
  <img src="https://img.shields.io/badge/License-MIT-green?style=flat-square" alt="License">
  <img src="https://img.shields.io/badge/Platform-iOS%20%7C%20macOS-lightgrey?style=flat-square" alt="Platform">
</p>

<p align="center">
  <b>Advanced Egern Proxy Configuration</b><br>
  <i>Smart Routing · Ad Blocking · Streaming Unlock · AI Service Optimization</i>
</p>

<p align="center">
  <a href="README_zh.md">简体中文</a> | <a href="README.md">English</a>
</p>

---

## Features

- **Smart DNS** — Bootstrap + DoH upstreams with domain-based forwarding
- **Ad Blocking** — Dual-layer blocking via AdBlack DNS + reject rulesets
- **Regional Routing** — 8 region groups with auto-filtered subscription nodes
- **Streaming Unlock** — Netflix, Disney+, YouTube, TikTok, Bilibili
- **AI Optimization** — Dedicated routing for OpenAI, Claude, Gemini, Bing
- **Protocol Support** — SS, Trojan, Vless, VMess, Hysteria 2, TUIC, WireGuard

## Quick Start

1. Download [`egern.yaml`](https://raw.githubusercontent.com/ClashConnectRules/Egern/refs/heads/main/egern.yaml)
2. Open **Egern** > **Profiles** > **Import**
3. Replace `https://your-subscription-url` in `AllServer` with your subscription link
4. Done — regional groups auto-pull nodes via `flatten: true`

---

## Table of Contents

- [Basic Settings](#basic-settings)
- [DNS Configuration](#dns-configuration)
- [Proxy Groups](#proxy-groups)
- [Rule Priority](#rule-priority)
- [Supported Protocols](#supported-protocols)
- [MITM Hostnames](#mitm-hostnames)
- [Installation](#installation)
- [Custom Icons](#custom-icons)
- [Rule Sources](#rule-sources)
- [Credits](#credits)
- [License](#license)

---

## Basic Settings

| Setting | Value | Description |
|:-------:|:-----:|:-----------|
| IPv6 | `true` | Enabled |
| VIF Only | `true` | Virtual interface mode |
| DNS Hijack | `*:53` | Hijack all DNS queries |
| GeoIP | [Masaiki GeoIP2-CN](https://github.com/Masaiki/GeoIP2-CN) | China GeoIP database |
| ASN DB | [P3TERX GeoLite](https://github.com/P3TERX/GeoLite.mmdb) | ASN database |
| Latency Test | `http://wifi.vivo.com.cn/generate_204` | Direct latency test URL |

---

## DNS Configuration

<details>
<summary><b>Bootstrap DNS</b></summary>

| Server | Provider |
|:------:|:--------:|
| `223.5.5.5` | Alibaba |
| `119.29.29.29` | Tencent |
| `1.12.12.12` | Alibaba (New) |
| `120.53.53.53` | ByteDance |
| `2400:3200::1` | CNNIC (IPv6) |

</details>

<details>
<summary><b>DoH Upstreams</b></summary>

| Name | Servers |
|:----:|:--------|
| `AdBlack` | `quic://dns.adguard-dns.com`, `https://dns.adguard-dns.com/dns-query` |
| `Alibaba` | `223.5.5.5`, `https://dns.alidns.com/dns-query` |
| `Tencent` | `119.29.29.29`, `https://doh.pub/dns-query` |
| `ByteDance` | `180.184.2.2`, `180.184.1.1` |
| `China` | Alibaba + Tencent combined |
| `Global` | Cloudflare, Google DNS |

</details>

<details>
<summary><b>DNS Forward Rules</b></summary>

| Match | Target | Description |
|:-----:|:------:|:-----------|
| `proxy_rule_set` (reject list) | AdBlack | Ad blocking DNS |
| `proxy_rule_set` (Alibaba) | Alibaba | Alibaba services |
| `proxy_rule_set` (Tencent) | Tencent | Tencent services |
| `proxy_rule_set` (DouYin) | ByteDance | ByteDance services |
| `proxy_rule_set` (Apple) | China | Apple services |
| `proxy_rule_set` (ChinaMax) | China | China domains |
| `proxy_rule_set` (Global) | Global | International domains |
| `*` | Global | Default fallback |

</details>

<details>
<summary><b>Host Mapping</b></summary>

| Service | DNS Server | Description |
|:-------:|:----------:|:-----------:|
| Taobao / Tmall / Alipay | `223.5.5.5` | Alibaba services |
| JD / QQ / WeChat | `119.28.28.28` | Tencent services |
| Bilibili / NetEase | `119.29.29.29` | Entertainment |
| Xiaomi | `119.29.29.29` | Xiaomi services |
| TestFlight | `8.8.4.4` | Apple TestFlight |
| Google (FCM / Download) | Special | Google services |
| Router Admin | System | Local routers |

</details>

All DNS queries (`*:53`) are hijacked to prevent leakage.

---

## Proxy Groups

### Core Groups

| Group | Type | Description |
|:-----:|:----:|:-----------|
| `AllServer` | `external` | All subscription nodes (auto-filter) |
| `Automatic` | `auto_test` | Regional auto-select (300s interval, 50ms tolerance) |
| `Proxy` | `select` | Proxy policy |
| `NoAuto` | `select` | Main entry point |
| `Mainland` | `select` | China Direct |

### Regional Groups

All regional groups use `flatten: true` + `filter` from AllServer, with `update_interval: 86400` (daily refresh).

| Group | Filter Keywords | Emoji |
|:-----:|:---------------:|:-----:|
| `Hong Kong` | HK, Hong Kong, HKG, MO | :flag_hk: |
| `Taiwan` | TW, Taiwan, TWN | :flag_tw: |
| `Japan` | JP, Japan, JPN | :flag_jp: |
| `Singapore` | SG, Singapore, SGP, MA | :flag_sg: |
| `United States` | US, USA, States, American | :flag_us: |
| `United Kingdom` | UK, England, Britain | :flag_gb: |
| `Korea` | KR, Korea, KOR | :flag_kr: |
| `Other` | Exclude above regions | :earth_africa: |

### Service Groups

All service groups include `AllServer` as a fallback option for manual node selection.

| Group | Policies | Purpose |
|:-----:|:--------:|:-------|
| `AI` | Automatic, US, JP, SG, AllServer | ChatGPT, Claude, Gemini, Bing |
| `Apple` | Mainland, HK, US, AllServer | Apple services |
| `Microsoft` | Mainland, HK, SG, US, AllServer | Microsoft services |
| `OneDrive` | Mainland, HK, SG, US, AllServer | Cloud storage |
| `Telegram` | Automatic, SG, US, HK, TW, JP, AllServer | Messaging |
| `X` | Automatic, HK, TW, SG, JP, US, AllServer | Twitter / X |
| `WeChat` | Mainland, HK, SG, US, AllServer | WeChat |
| `Netflix` | HK, TW, SG, JP, US, AllServer | Netflix streaming |
| `Disney+` | HK, SG, AllServer | Disney+ streaming |
| `YouTube` | Automatic, HK, TW, SG, JP, US, AllServer | YouTube streaming |
| `TikTok` | TW, SG, JP, US, AllServer | TikTok unlock |
| `Bilibili` | Mainland, HK, TW, AllServer | Bilibili (HK/TW unlock) |
| `Speedtest` | Mainland, Automatic, AllServer | Speed test |

### Group Dependency

```
AllServer (subscription)
  └─ Regional Groups (HK, TW, JP, SG, US, UK, KR, Other)  [flatten]
       ├─ Automatic (auto_test)
       └─ Service Groups (AI, Apple, Netflix, ...)
            └─ All include AllServer as fallback
```

---

## Rule Priority

```
 1. Unbreak Rules     Fix broken connections > DIRECT
 2. Ad Blocking       SKK Ruleset > REJECT
 3. Privacy           Block trackers
 4. CN Apps           WeChat, NetEase, Bilibili, Weibo
 5. Apple Services    App Store, Apple News, Apple TV
 6. AI Services       OpenAI, Claude, Gemini, Bing
 7. Streaming         Disney+, Netflix, TikTok, YouTube
 8. Regional Unlock   US, EU, JP, KR, HK, TW streams
 9. Social Media      Twitter, Telegram, Facebook, Instagram
10. Other Global      OneDrive, Microsoft, GitHub, Speedtest
11. CN Rules          SKK + ChinaMax ruleset
12. Global Rules      CDN, Global ruleset
13. LAN               Local network > DIRECT
14. GeoIP             CN > Mainland
15. Final Rule        default > NoAuto
```

---

## Supported Protocols

| Protocol | Support |
|:---------|:-------:|
| Shadowsocks | Full |
| Trojan | Full |
| Vless | Full (with transport) |
| VMess | Full (with transport) |
| Hysteria 2 | Full |
| TUIC | Full |
| SOCKS5 | Full |
| HTTP | Full |
| WireGuard | Full |

---

## MITM Hostnames

- `www.google.cn`
- `api.abema.io`
- `*.zhihu.com`
- `sub.store`

MITM is required for URL rewrite and header rewrite features.

---

## Installation

### Method 1: Import in App

1. Download `egern.yaml`
2. **Egern** > **Profiles** > **Import**
3. Select the downloaded file

### Method 2: iCloud Sync

1. Save `egern.yaml` to iCloud Drive
2. **Egern** > **Profiles** > **Import from iCloud**
3. Select the file

### Configure Subscription

Replace the placeholder URL in the `AllServer` external group:

```yaml
- external:
    name: AllServer
    type: select
    urls:
      - "https://your-subscription-url"
    filter: '^((?!Remain|Expired|官网|如需|套餐|去除|剩余|距离|Reset|重置|流量).)+$'
    update_interval: 86400
```

Only the `AllServer` group needs the subscription URL. Regional groups automatically pull nodes from AllServer via `flatten: true`.

---

## Custom Icons

Each policy group supports a custom `icon` field:

```yaml
- select:
    name: Telegram
    policies:
      - Automatic
      - Singapore
    icon: "https://example.com/icon.png"
```

Icons should be **PNG format**, recommended size **120x120 px**.

<details>
<summary><b>Recommended Icon Packs</b></summary>

| Icon Pack | Link |
|:---------:|:-----|
| Qure (Color) | [QureColor-All.json](https://raw.githubusercontent.com/Koolson/Qure/master/Other/QureColor-All.json) |
| Orz-3 (Color) | [miniColor.json](https://raw.githubusercontent.com/Orz-3/mini/master/miniColor.json) |
| tugepaopao | [Cute.json](https://raw.githubusercontent.com/tugepaopao/Image-Storage/master/other/Cute.json) |
| Semporia | [Semporia.json](https://raw.githubusercontent.com/Semporia/Hand-Painted-icon/master/Semporia.json) |

</details>

---

## Rule Sources

| Source | Description |
|:------:|:-----------|
| [blackmatrix7](https://github.com/blackmatrix7/ios_rule_script) | Cross-platform rules |
| [Skk.moe](https://ruleset.skk.moe) | SKK ruleset |
| [VirgilClyne](https://github.com/VirgilClyne/GetSomeFries) | ASN rules |
| [Semporia](https://github.com/Semporia/TikTok-Unlock) | TikTok unlock |
| [zxfccmm4](https://github.com/zxfccmm4) | Unbreak rules |
| [Loyalsoldier](https://github.com/Loyalsoldier/surge-rules) | Reject ruleset |

---

## Credits

- [Egern](https://egernapp.com)
- [blackmatrix7](https://github.com/blackmatrix7)
- [Skk.moe](https://github.com/Skk.moe)
- [lige47/QuanX-icon-rule](https://github.com/lige47/QuanX-icon-rule)

---

## License

[MIT](LICENSE)

---

<p align="center">
  <sub>Made with heart for better internet experience</sub>
</p>
