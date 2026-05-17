# Egern Configuration

<p align="center">
  <img src="https://img.shields.io/badge/Egern-1-blue?style=flat-square" alt="Egern">
  <img src="https://img.shields.io/badge/License-MIT-green?style=flat-square" alt="License">
</p>

<p align="center">
  <b>Advanced Egern Proxy Configuration</b><br>
  <i>Smart Routing, Ad Blocking, Streaming Unlock</i>
</p>

<p align="center">
  <a href="#-basic-settings">Basic</a> &bull;
  <a href="#-dns-configuration">DNS</a> &bull;
  <a href="#-proxy-groups">Groups</a> &bull;
  <a href="#-rule-priority">Rules</a> &bull;
  <a href="#-installation">Install</a>
</p>

<p align="center">
  <b>Language / 语言切换</b><br>
  <a href="README_zh.md">简体中文</a> | <a href="README.md">English</a>
</p>

---

## Download

| File | Link |
|:----:|:-----|
| **egern.yaml** | [Download](https://raw.githubusercontent.com/ClashConnectRules/Egern/refs/heads/main/egern.yaml) |

---

## Basic Settings

| Setting | Value | Description |
|:-------:|:-----:|:-----------|
| HTTP Port | `6152` | HTTP proxy port |
| SOCKS5 Port | `6153` | SOCKS5 proxy port |
| IPv6 | `false` | Disabled by default |
| QUIC Block | `false` | Not blocked |
| GeoIP | [Hackl0us GeoIP2-CN](https://github.com/Hackl0us/GeoIP2-CN) | China GeoIP database |

---

## DNS Configuration

### Bootstrap DNS

| Server | Provider |
|:------:|:--------:|
| `223.5.5.5` | Alibaba |
| `223.6.6.6` | Alibaba |
| `119.29.29.29` | Tencent |

### DoH Upstreams

| Name | URL |
|:----:|:-----|
| `alidns` | `https://dns.alidns.com/dns-query` |
| `dohpub` | `https://doh.pub/dns-query` |

### DNS Forward Rules

| Match | Target |
|:-----:|:------:|
| `*.cn` | Bootstrap (China DNS) |
| `*` | DoH Public |

### DNS Hijack

Targets: `8.8.8.8:53`, `8.8.4.4.4:53` (Google DNS hijacked to prevent leakage)

### Host Mapping

| Service | DNS Server | Description |
|:-------:|:----------:|:-----------:|
| Taobao / Tmall / Alipay | `223.5.5.5` | Alibaba services |
| JD / QQ / WeChat | `119.28.28.28` | Tencent services |
| Bilibili / NetEase | `119.29.29.29` | Entertainment |
| Xiaomi | `119.29.29.29` | Xiaomi services |
| TestFlight | `8.8.4.4` | Apple TestFlight |
| Google (FCM / Download) | Special | Google services |
| Router Admin | System | Local routers |

---

## Proxy Groups

### Core Groups

| Group | Type | Description |
|:-----:|:----:|:-----------|
| `Mainland` | `select` | China Direct |
| `NoAuto` | `select` | Main entry point |
| `Automatic` | `auto_test` | Regional auto-select |
| `AllServer` | `select` | All subscription nodes |
| `Proxy` | `select` | Proxy policy |

### Regional Groups (Auto Test)

| Group | Filter | Interval | Tolerance |
|:-----:|:------:|:--------:|:---------:|
| `Hong Kong` | `HK\|Hong\|香港\|港` | 300s | 50ms |
| `Taiwan` | `TW\|Tai\|台湾\|台` | 300s | 50ms |
| `Japan` | `JP\|Japan\|日本\|日` | 300s | 50ms |
| `Singapore` | `SG\|Singapore\|新加坡\|狮城` | 300s | 50ms |
| `United States` | `US\|States\|美国\|美` | 300s | 50ms |
| `United Kingdom` | `UK\|United Kingdom\|英国` | 300s | 50ms |
| `Korea` | `KR\|Korea\|韩国\|韩` | 300s | 50ms |
| `Other` | Exclude above | 300s | 50ms |

### Service Groups

| Group | Default | Purpose |
|:-----:|:-------:|:-------|
| `AI` | Automatic | ChatGPT, Claude, Gemini, Bing |
| `Apple` | Mainland - HK - US | Apple services |
| `Microsoft` | Mainland - HK - SG - US | Microsoft services |
| `OneDrive` | Mainland - HK - SG - US | Cloud storage |
| `Telegram` | Automatic - SG - US - HK | Messaging |
| `X` | Automatic - HK - TW - SG - JP - US | Twitter / X |
| `WeChat` | Mainland - HK - SG - US | WeChat |
| `Netflix` | HK - TW - SG - JP - US | Netflix streaming |
| `Disney+` | HK - SG | Disney+ streaming |
| `YouTube` | Automatic - HK - TW - SG - JP - US | YouTube streaming |
| `TikTok` | TW - SG - JP - US | TikTok unlock |
| `Bilibili` | Mainland - HK - TW | Bilibili (HK/TW unlock) |
| `Speedtest` | Mainland - Auto - AllServer | Speed test |

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
14. Final Rule        default > NoAuto
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

## Special Features

### URL Rewrite

| Original | Target | Status |
|:--------:|:------:|:------:|
| `google.cn` | `google.com` | 302 |
| `maps.google.cn` | `maps.google.com` | 302 |
| `taobao.com` | HTTPS | 302 |
| `jd.com` | HTTPS | 302 |
| `mi.com` | HTTPS | 302 |
| `you.163.com` | HTTPS | 302 |
| `suning.com` | HTTPS | 302 |
| `yhd.com` | HTTPS | 302 |
| `api.abema.io` | Reject | -1 |

### Header Rewrite

| Target | Header | Value | Type |
|:------:|:------:|:-----:|:----:|
| `github.com` | Accept-Language | en-us | request |
| `*.githubusercontent.com` | Accept-Language | en-us | request |

Fixes GitHub 429 rate limit issue.

### MITM Hostnames

- `www.google.cn`
- `api.abema.io`
- `*.zhihu.com`
- `sub.store`

---

## Installation

### Method 1: Import in App

```
1. Download egern.yaml
2. Egern > Profiles > Import
3. Select the downloaded file
```

### Method 2: iCloud Sync

```
1. Save egern.yaml to iCloud Drive
2. Egern > Profiles > Import from iCloud
3. Select the file
```

### Configure Subscription

Replace the placeholder URL in all `external` policy groups:

```yaml
policy_groups:
  - external:
      name: AllServer
      type: select
      urls:
        - "https://your-subscription-url"
```

Update the `urls` field in every `external` group (Hong Kong, Taiwan, Japan, Singapore, United States, United Kingdom, Korea, Other).

---

## Configuration

### Add Proxy Servers

Edit the `proxies` section:

```yaml
proxies:
  - shadowsocks:
      name: MySS
      method: aes-256-gcm
      password: your_password
      server: 1.2.3.4
      port: 8388

  - trojan:
      name: MyTrojan
      server: example.com
      port: 443
      password: your_password

  - vmess:
      name: MyVMess
      server: 1.2.3.4
      port: 443
      user_id: uuid
      security: auto

  - hysteria2:
      name: MyHysteria2
      server: 1.2.3.4
      port: 443
      auth: your_password
```

### Enable MITM

```yaml
mitm:
  ca_p12: "your_base64_cert"
  ca_passphrase: "123456"
  hostnames:
    includes:
      - "*.example.com"
```

---

## Rule Sources

| Source | Description |
|:------:|:-----------|
| [blackmatrix7](https://github.com/blackmatrix7/ios_rule_script) | Cross-platform rules |
| [Skk.moe](https://ruleset.skk.moe) | SKK ruleset |
| [VirgilClyne](https://github.com/VirgilClyne/GetSomeFries) | ASN rules |
| [Semporia](https://github.com/Semporia/TikTok-Unlock) | TikTok unlock |
| [zxfccmm4](https://github.com/zxfccmm4) | Unbreak rules |

---

## Notes

| Item | Description |
|:----:|:-----------|
| Subscription | Replace with your own subscription URL |
| Rule Update | Rules auto-update from online sources |
| Speed Test | 300s interval, 3s timeout, 50ms tolerance |
| MITM Cert | Required for URL/header rewrite |
| Node Filter | Auto-filter nodes with "traffic/reset/expire" keywords |

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

### Recommended Icon Packs

| Icon Pack | Link |
|:---------:|:-----|
| Qure (Color) | [QureColor-All.json](https://raw.githubusercontent.com/Koolson/Qure/master/Other/QureColor-All.json) |
| Orz-3 (Color) | [miniColor.json](https://raw.githubusercontent.com/Orz-3/mini/master/miniColor.json) |
| tugepaopao | [Cute.json](https://raw.githubusercontent.com/tugepaopao/Image-Storage/master/other/Cute.json) |
| Semporia | [Semporia.json](https://raw.githubusercontent.com/Semporia/Hand-Painted-icon/master/Semporia.json) |

---

## Credits

- [Egern](https://egernapp.com)
- [blackmatrix7](https://github.com/blackmatrix7)
- [Skk.moe](https://github.com/Skk.moe)
- [lige47/QuanX-icon-rule](https://github.com/lige47/QuanX-icon-rule)

---

## License

**MIT**

---

<p align="center">
  <sub>Made with heart for better internet experience</sub>
</p>
