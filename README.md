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
| IPv6 | `true` | Enabled |
| VIF Only | `true` | Virtual interface mode |
| DNS Hijack | `*:53` | Hijack all DNS queries |
| GeoIP | [Masaiki GeoIP2-CN](https://github.com/Masaiki/GeoIP2-CN) | China GeoIP database |
| ASN DB | [P3TERX GeoLite](https://github.com/P3TERX/GeoLite.mmdb) | ASN database |
| Latency Test | `http://wifi.vivo.com.cn/generate_204` | Direct latency test URL |

---

## DNS Configuration

### Bootstrap DNS

| Server | Provider |
|:------:|:--------:|
| `223.5.5.5` | Alibaba |
| `119.29.29.29` | Tencent |
| `1.12.12.12` | Alibaba (New) |
| `120.53.53.53` | ByteDance |
| `2400:3200::1` | CNNIC (IPv6) |

### DoH Upstreams

| Name | Servers |
|:----:|:--------|
| `AdBlack` | `quic://dns.adguard-dns.com`, `https://dns.adguard-dns.com/dns-query` |
| `Alibaba` | `223.5.5.5`, `https://dns.alidns.com/dns-query` |
| `Tencent` | `119.29.29.29`, `https://doh.pub/dns-query` |
| `ByteDance` | `180.184.2.2`, `180.184.1.1` |
| `China` | Alibaba + Tencent combined |
| `Global` | Cloudflare, Google DNS |

### DNS Forward Rules

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

### DNS Hijack

All DNS queries (`*:53`) are hijacked to prevent leakage.

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
| `AllServer` | `external` | All subscription nodes (auto-filter) |
| `Automatic` | `auto_test` | Regional auto-select |
| `Proxy` | `select` | Proxy policy |
| `NoAuto` | `select` | Main entry point |
| `Mainland` | `select` | China Direct |

### Regional Groups (Select + Flatten)

| Group | Filter Keywords | Emoji |
|:-----:|:---------------:|:-----:|
| `Hong Kong` | HK, Hong Kong, HKG | 🇭🇰 |
| `Taiwan` | TW, Taiwan, TWN | 🇹🇼 |
| `Japan` | JP, Japan, JPN | 🇯🇵 |
| `Singapore` | SG, Singapore, SGP | 🇸🇬 |
| `United States` | US, USA, States, American | 🇺🇸 |
| `United Kingdom` | UK, England, Britain | 🇬🇧 |
| `Korea` | KR, Korea, KOR | 🇰🇷 |
| `Other` | Exclude above regions | 🌍 |

All regional groups use `flatten: true` + `filter` from AllServer, with `update_interval: 86400` (daily refresh).

### Service Groups

| Group | Policies | Purpose |
|:-----:|:--------:|:-------|
| `AI` | Automatic, US, JP, SG | ChatGPT, Claude, Gemini, Bing |
| `Apple` | Mainland, HK, US | Apple services |
| `Microsoft` | Mainland, HK, SG, US | Microsoft services |
| `OneDrive` | Mainland, HK, SG, US | Cloud storage |
| `Telegram` | Automatic, SG, US, HK, TW, JP | Messaging |
| `X` | Automatic, HK, TW, SG, JP, US | Twitter / X |
| `WeChat` | Mainland, HK, SG, US | WeChat |
| `Netflix` | HK, TW, SG, JP, US | Netflix streaming |
| `Disney+` | HK, SG | Disney+ streaming |
| `YouTube` | Automatic, HK, TW, SG, JP, US | YouTube streaming |
| `TikTok` | TW, SG, JP, US | TikTok unlock |
| `Bilibili` | Mainland, HK, TW | Bilibili (HK/TW unlock) |
| `Speedtest` | Mainland, Automatic, AllServer | Speed test |

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

## Notes

| Item | Description |
|:----:|:-----------|
| Subscription | Replace with your own subscription URL in AllServer |
| Rule Update | Rules and nodes auto-update from online sources |
| Speed Test | 300s interval, 3s timeout, 50ms tolerance |
| Node Filter | Auto-filter nodes with "traffic/reset/expire" keywords |
| DNS Hijack | All DNS queries hijacked to prevent leakage |
| Ad Blocking | AdBlack DNS + reject rule set for comprehensive blocking |

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
